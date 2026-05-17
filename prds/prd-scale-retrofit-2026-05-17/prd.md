# Glimmer Scale Retrofit PRD

## Document Control

| Field | Value |
| --- | --- |
| Title | Glimmer Scale Retrofit (Q2/Q3 2026) |
| Status | Draft, in review |
| Author | Staff Eng (Platform) with VP Eng |
| Reviewers | CTO, Eng Manager (Feed), Eng Manager (Media), SRE Lead, Finance Ops |
| Version | 0.3 |
| Last updated | 2026-05-17 |
| Type | PRD leaning RFC (architectural remediation) |
| Related docs | INC-2026-04-19 (feed brownout), FIN-2026-Q2 (cloud spend), ROAD-2026 (product roadmap, frozen for this work) |

## The Forcing Function

On 2026-03-04, a creator with 2.1M TikTok followers posted a 47-second tour of their Glimmer boards. The video crossed 9M views in 72 hours. Installs went from a 7-day average of 1,840/day to a peak of 38,400/day on 2026-03-09. We have sustained 14K to 22K installs/day since.

The result, over five weeks: 12K MAU to 180K MAU. WAU now sits at 96K, up from 4.1K. We have been brownout-adjacent on the home feed every weekday between 18:00 and 22:00 ET since 2026-03-12. We had one full feed outage on 2026-04-19 (43 minutes, INC-2026-04-19). Our app store rating has dropped from 4.6 to 4.1 over the same window, driven by latency and upload-failure reviews.

We are not throttling growth. The creator is still actively posting. The retention curve at week 4 is 31%, which is the best we have ever seen. This document plans the remediation that lets us absorb the next 5x without burning the company down on AWS spend or losing the cohort.

## Current State

Measured at peak load (19:30 ET, 2026-05-14):

| Metric | 2024 baseline | 2026-05-14 peak | Notes |
| --- | --- | --- | --- |
| WAU | 4.1K | 96K | 23x |
| Home feed p50 | 180ms | 1.6s | Below the fold often blank |
| Home feed p99 | 410ms | 4.2s | Triggers client-side timeout retries |
| Search p95 | 220ms | 14s | Many queries time out at the gateway (10s) |
| Cold-start mobile p50 | 1.4s | 6.1s | App Store reviews call this out by name |
| Image upload p95 (12MB) | 3.1s | 28s | Frequent client retries, duplicate uploads |
| Signup success rate | 99.4% | 88.2% | Backpressure from auth service |
| AWS monthly run-rate | $4.8K | $42K | Image egress and RDS reads dominate |

Operational pain: on-call has been paged 31 times in the last 30 days. Six of those pages were the same shape (RDS CPU saturation on the search replica). We are running RDS at 92% steady-state CPU during peak hours.

## Bottleneck Analysis

We have four subsystems that are visibly past their design envelope, and one that will be next.

### 1. Home feed query

The feed is computed on read. For each request, we join `users -> follows -> boards -> pins` and order by `pin.created_at`. The query is O(N) on the number of boards a user follows. At 12K WAU the average user followed 14 boards. At 96K WAU the heavy cohort (top decile) follows 380+ boards. The query planner falls off a cliff at roughly 120 boards followed. The query is the single largest source of RDS read load (about 58% of read IOPS).

### 2. Image encoder

Each upload host runs a single FFmpeg process to produce the five derived sizes (thumb, small, medium, large, hero). The encoder is CPU-bound and serialized. At more than 3 concurrent uploads per host, the queue depth on the host backs up and the API gateway times out the upload at 30s. We have 8 upload hosts, so the effective ceiling is roughly 24 concurrent uploads platform-wide. Peak concurrent uploads on 2026-05-14 was 71.

### 3. Search

Search is Postgres FTS on a single RDS instance (db.r5.2xlarge) that also serves the user and board tables. The FTS index is 41GB. The instance is hot: 92% CPU at peak, with the FTS queries accounting for the majority of that. p95 query latency is 14s. There is no sharding. There is no replica dedicated to search.

### 4. Auth service

The auth service runs on a single EC2 instance (m5.large) behind an ALB. Session creation writes to a local in-memory cache, which is why we cannot horizontally scale it without a state change. At peak signup load (about 45 signups/minute), it queues. Signup success rate has dropped to 88.2%, with most failures being client timeouts after the auth service has accepted the request.

### 5. Worker queue (next to fail)

All async work (notifications, derived image generation, feed cache invalidation) runs through a single SQS queue in us-east-1. Latency from non-US users is already visibly higher (notification delivery p95 from EU users is 11s). When us-east-1 has a partial outage, all async work stops globally. We have not had this fail in production yet. We expect we will.

### 6. CDN economics

Image bandwidth is 71% of the $42K run-rate. We serve every derived size from origin on cache miss, and our cache hit rate is 64%. We do not revoke signed URLs on delete, so deleted images continue to be served until the URL expires (24 hours). We have no aggressive policy on the derived sizes the client does not actually use.

## Target State

The new envelope, by end of the retrofit (90 days):

| Metric | Current | Target | Milestone |
| --- | --- | --- | --- |
| Home feed p99 | 4.2s | < 400ms | M2 |
| Home feed p50 | 1.6s | < 150ms | M2 |
| Search p95 | 14s | < 600ms | M1 |
| Cold-start p50 | 6.1s | < 2s | M2 |
| Image upload p95 (12MB) | 28s | < 8s | M3 |
| Signup success rate | 88.2% | > 99.0% | M1 |
| AWS monthly run-rate | $42K | < $26K | M3 (90 days from start) |
| On-call pages / 30 days | 31 | < 8 | M3 |

These numbers are the gates. Milestones do not close until the numbers hold for seven consecutive days at peak load.

## Required Changes

Each item below is a capability the retrofit must deliver. They are framed as scale interventions, not features.

### C1. Pre-computed home feed (fan-out on write)

Move from compute-on-read to materialized per-user feed. A user's feed is stored as an ordered list of pin IDs in Redis, capped at 500 items. On a new pin, a worker fans out the pin ID to every follower's feed list. On a board update, the worker recomputes affected users. Read path becomes a Redis ZRANGE plus a batched pin fetch. Cron sweep nightly to reconcile drift. Write amplification is the cost. We have modeled it: at current follower distribution, an average pin triggers 340 writes. Peak write QPS estimate: 4,200. Redis cluster sized for 10x that.

### C2. Search on sharded OpenSearch

Retire Postgres FTS. Stand up a 3-node OpenSearch cluster, sharded by pin ID hash. Dual-write during cutover (writes go to both Postgres FTS and OpenSearch for 14 days). Read traffic shifts 10% / 50% / 100% over the cutover window. Postgres FTS index dropped on completion. Expected RDS CPU reduction: 35 to 45 points at peak.

### C3. Multi-region worker queue

Replace single SQS in us-east-1 with SNS topic plus per-region SQS subscriptions (us-east-1, us-west-2, eu-west-1). Workers in each region consume their regional queue. Cross-region failover via SNS re-subscription. Notification latency target: p95 < 2s globally. This also removes the single-region failure mode for all async work.

### C4. Fargate-based image encoder pool

Replace the single-process FFmpeg encoder with a Fargate task pool. Size-class routing: thumb and small to a lightweight task (0.5 vCPU, 1GB), medium and large to standard (2 vCPU, 4GB), hero to a beefy task (4 vCPU, 8GB). Autoscale on queue depth. Concurrent upload ceiling moves from 24 to roughly 800 (and elastically beyond).

### C5. CDN cost reduction

Three changes here. (a) Aggressive cache policy on derived sizes: TTL 30 days, stale-while-revalidate 7 days. (b) Generate derived sizes lazily on first request rather than eagerly on upload. About 38% of derived sizes are never requested. (c) Signed-URL revocation on delete, with a short-TTL revocation cache at the edge. Expected savings: $9K to $12K/mo.

### C6. Horizontally scalable auth

Move session state from per-instance in-memory cache to a shared Redis (separate cluster from feed). Stand up 3 auth instances behind the existing ALB. Session tokens become opaque references to Redis-stored sessions. Existing JWTs remain valid through their natural expiry (no forced re-auth).

## Migration Approach

The product cannot have a maintenance window. Every change ships behind a feature flag and rolls out by user-ID hash bucket.

| Phase | Scope | Duration | Backout |
| --- | --- | --- | --- |
| M1 (Stop the bleeding) | C2 (search), C6 (auth) | Weeks 1 to 3 | Flag flip, traffic returns to Postgres FTS / single auth |
| M2 (Feed and client) | C1 (feed), client cold-start work | Weeks 4 to 7 | Flag flip, read path returns to compute-on-read |
| M3 (Media and cost) | C3 (queue), C4 (encoder), C5 (CDN) | Weeks 8 to 12 | Per-component flag flip; encoder backout returns to FFmpeg hosts |

Each rollout uses 1% / 5% / 25% / 50% / 100% buckets. Each step holds for 24 hours minimum, with explicit metric gates. A failed gate auto-pauses the rollout and pages the owning team.

Backwards compatibility: existing mobile clients (down to app version 4.8, ~14% of installed base) must continue to work without forced upgrade. The API contract does not change. All migrations are server-side.

## Risks and Rollback

### High-risk items

| Risk | Likelihood | Blast radius | Mitigation |
| --- | --- | --- | --- |
| Feed fan-out write amplification storms Redis on a viral pin | Medium | Feed unavailable for affected followers | Cap fan-out at 50K immediate, queue the long tail; circuit breaker on Redis write QPS |
| OpenSearch dual-write gets out of sync | Medium | Search returns stale or missing results | Daily reconciliation job; ability to re-index from canonical Postgres source |
| Lazy derived-size generation adds latency to first-view | High (will happen) | First viewer waits up to 1.2s for medium-size derive | Acceptable. Pre-generate thumb and small eagerly; rest lazily |
| Auth session migration loses active sessions | Low | Users see a forced login | Dual-read window of 7 days; only invalidate after confirmed migration |
| Fargate cold-start spikes during traffic surges | Medium | Upload p95 temporarily spikes | Warm pool of 20% baseline capacity; pre-warm before scheduled creator events |

### Rollback posture

Every capability has a flag and a documented backout runbook. The backout for C1 (feed) is the most expensive: we must keep the compute-on-read path warm and tested through M3. We accept the RDS load cost of that during the transition.

## Non-Goals

We are calling these out explicitly because the fire is loud and people are pitching adjacent work.

- Not redesigning the product surface area. The UI does not change. The information architecture does not change. The roadmap is frozen for the duration of this retrofit.
- Not adding new features. No new sharing surfaces, no new board types, no creator monetization.
- Not migrating off AWS. A board member suggested GCP after seeing the bill. The lift is 9 to 12 months, the savings are unproven at our shape, and it does not solve any of the bottlenecks above. Rejected.
- Not rewriting in another language. One engineer floated a Rust rewrite of the encoder. The Fargate-with-FFmpeg approach hits the SLO without a rewrite. Rejected for risk and timeline.
- Not consolidating to a single database. The microservices keep their own stores. The retrofit shards and offloads where needed; it does not re-architect ownership.
- Not adding paid-tier monetization in parallel. The growth team has a separate proposal. It happens after M3, on a separate roadmap.

If any of these come back up during the retrofit, the answer is "after M3, in a separate planning cycle."

## Success Criteria

The retrofit is complete when all of the following hold for seven consecutive days at peak load:

1. Home feed p99 < 400ms, p50 < 150ms
2. Search p95 < 600ms
3. Cold-start mobile p50 < 2s
4. Image upload p95 (12MB photo) < 8s
5. Signup success rate > 99.0%
6. AWS monthly run-rate trending under $26K
7. On-call pages on these subsystems < 8 in the trailing 30 days

Customer-facing canaries (leading indicators we also watch):

- App Store rating recovers to 4.4 or higher (currently 4.1)
- Support tickets tagged "slow", "stuck upload", or "can't find" drop by 70% from the 2026-04 baseline
- Week-4 retention holds at or above 28% (currently 31%, do not regress)

If the engineering numbers hit but the customer canaries do not, we pause and investigate before declaring done.

## Open Issues

1. Redis cluster sizing for C1. The write-amp model has a wide confidence interval. We need a load test before committing to the cluster shape. Owner: Feed EM. Due: end of week 2.
2. OpenSearch cost. The 3-node cluster is roughly $3.2K/mo. We have not yet confirmed this is recouped by RDS savings. Owner: SRE Lead. Due: end of M1.
3. Lazy-derive UX. Product wants to know what users see during the 600ms to 1.2s derive window on first view. Placeholder spec needs sign-off. Owner: Mobile lead. Due: week 4.
4. EU region for C3. We have no EU presence today. Standing up eu-west-1 raises data-residency questions we have deferred. Legal review pending. Owner: VP Eng. Due: week 6.
5. Should we add a public status page during the retrofit? Currently we have nothing customer-facing for incidents. Probably yes. Owner: VP Eng. Due: decide by end of week 1.
6. Creator communication. The TikTok creator who drove the growth has asked twice when uploads will "stop being broken." We owe them a direct answer. Owner: Founder. Due: this week.
