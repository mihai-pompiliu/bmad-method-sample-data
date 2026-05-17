# PRD / RFC: Aurora Migration

**Document Type:** Hybrid PRD + Architecture RFC
**Status:** Draft v0.9 (for ARB review)
**Author:** Principal Engineer, Platform
**Date:** 2026-05-17
**Reviewers:** Architecture Review Board, VP Eng, CTO, SRE Lead, Security
**Project Codename:** Aurora
**Estimated Duration:** 9 months (Q3 2026 - Q1 2027)

---

## 1. Executive Summary

Helios (our flagship B2B SaaS billing and account management platform) is anchored on a 600k-LOC Ruby on Rails 7.1 monolith we have internally nicknamed "Big Hank." Big Hank has served us well for nine years and currently processes ~12M API requests/day across 2,400 enterprise tenants. However, by every meaningful engineering productivity metric we track, Big Hank is now the single largest tax on velocity, reliability, and cloud spend at the company. Deploy contention, a 47-minute CI pipeline, and a blast radius that routinely takes down unrelated product surfaces during incidents have pushed our engineering org into a state of learned helplessness. The cost of doing nothing is no longer "slower velocity"; it is attrition, customer churn from reliability incidents, and a runaway AWS bill that has grown 38% YoY while revenue has grown 22% YoY.

Aurora is a 9-month program to decompose Big Hank into approximately 12 domain-aligned microservices using the Strangler Fig pattern. The target architecture is Go services running on Amazon EKS, with gRPC for internal east-west traffic, a GraphQL gateway (Apollo Router) for north-south client traffic, Postgres-per-service (no shared databases), and Kafka for asynchronous domain events. We are explicitly choosing Go over Kotlin, Rust, and continued Ruby for reasons enumerated in Section 9 (Non-Goals) and Appendix A. We are NOT pursuing a "big bang" rewrite; we are extracting services one bounded context at a time, with each extraction gated on production traffic shadowing, dual-write validation, and a documented rollback procedure.

The three forcing functions for Aurora are: (1) **deploy cadence**, currently 1 production deploy per week (often skipped), with a target of 3 deploys per day per service; (2) **team scaling pain**, where 40 engineers across 7 squads are now contending for the same merge queue, the same staging environment, and the same shared Sidekiq worker pool; and (3) **AWS bill optimization**, where Big Hank's 80+ over-provisioned EC2 instances and a single 16xlarge RDS Postgres primary represent ~$340k/month of spend that should be closer to $180k/month for our actual workload shape.

We expect Aurora to be disruptive. We expect to take temporary reliability regressions during cutover windows. We expect at least one service extraction to be reverted. The success criteria in Section 11 reflect the end state at month 9, not month 3. The ARB is asked to approve the program, the phasing, the target architecture, and the staffing model (a dedicated 6-engineer Platform team plus embedded migration leads in each product squad). Approval of specific service extractions will happen at the per-service RFC level, with this document as the parent contract.

---

## 2. Current State

### 2.1 The Monolith

Big Hank is a Rails 7.1 application backed by:
- A single Postgres 14 RDS instance (db.r6i.16xlarge, 512 GiB RAM, ~3.8 TiB of data)
- A Redis 6 cluster for caching, session storage, and Sidekiq queues
- ~280 Sidekiq workers spread across 18 EC2 instances (m6i.4xlarge)
- ~60 Puma web instances behind an ALB
- A bespoke "feature flag" system implemented as a `flags` table with 1,400 active flags, most undocumented
- 14,000 RSpec files, 87,000 examples, full-suite runtime of 47 minutes on our largest CI runner tier

The codebase has accreted across 9 years and three major product pivots. There is no enforced module boundary; `app/models/` contains 612 ActiveRecord classes, the largest of which (`Account`) has 4,800 lines and 71 `has_many` associations. Cross-domain joins are pervasive; a single GraphQL query for the customer dashboard touches 23 tables across what should be 6 distinct domains.

### 2.2 Deploy Contention

We deploy Big Hank to production once per week, on Wednesdays at 14:00 PT, behind a 4-hour change window. In Q1 2026:
- 11 of 13 scheduled deploys went out on time
- 2 were rolled back within the change window (one auth regression, one billing rounding bug)
- The mean PR-merge-to-production lead time was **9.4 days**
- The merge queue (Mergify) had a median wait of 3.2 hours during peak hours, with a p95 of 11.7 hours
- 18% of merge queue runs failed due to flaky tests, requiring re-runs

Squads have responded to this by batching changes, which makes each deploy larger, which makes each deploy riskier, which justifies the slow cadence — a textbook reinforcing loop.

### 2.3 Test Suite Pathology

The 47-minute full-suite runtime is the headline number, but the deeper issue is the **flakiness budget**. We currently tolerate a ~2.1% flake rate per CI run, which means roughly 1 in 50 PR runs fails for reasons unrelated to the change. Engineers have learned to mash "retry" reflexively, which both masks real regressions and burns CI compute (~$28k/month in GitHub Actions and self-hosted runner spend).

The slowest 200 specs account for 31% of total runtime. The slowest single spec (`spec/services/billing/invoice_finalizer_spec.rb`) takes 6.4 minutes alone due to a fixture cascade that instantiates the entire account graph.

### 2.4 Blast Radius Incidents

In the trailing 12 months, we had 14 SEV-2 or higher incidents. Of these:
- 9 were caused by a change in one domain breaking an unrelated domain (e.g., a notification template change that triggered a billing recalculation cascade)
- 4 were caused by Sidekiq queue saturation, where a single misbehaving job class starved unrelated workers
- 1 was caused by the RDS primary running out of connections during a marketing email send

The shared Sidekiq pool and shared Postgres connection pool are the two largest blast-radius amplifiers.

### 2.5 On-Call Pain

The primary on-call rotation covers Big Hank in its entirety. The on-call engineer is expected to triage incidents across billing, accounts, notifications, reporting, integrations, and the admin surface — six bounded contexts that no single engineer has deep knowledge of. The mean time-to-page-the-right-team during incidents is 18 minutes, which is most of our SEV-2 MTTR budget. Voluntary attrition on the on-call rotation is 2.3x the rest of engineering.

### 2.6 AWS Spend

Current monthly run-rate (production only):
- RDS (db.r6i.16xlarge primary + 2 read replicas): $61k
- EC2 (Puma + Sidekiq + supporting): $148k
- ElastiCache Redis: $14k
- Data transfer + NAT: $39k
- CloudWatch + logging egress to Datadog: $52k
- S3 + misc: $26k
- **Total: ~$340k/month**

The RDS instance runs at 22% average CPU and 41% memory utilization. We are paying for headroom we use only during marketing campaign send windows. A workload-shaped architecture (Postgres-per-service, right-sized per domain) should reduce database spend by ~45%.

---

## 3. Target State

### 3.1 Architecture Overview

- **Compute:** Amazon EKS, one cluster per environment (dev, staging, prod), Karpenter for node autoscaling
- **Language:** Go 1.23 for all new services; Rails frozen at 7.1 for the residual monolith
- **East-west RPC:** gRPC over HTTP/2, with `buf` for schema management and `connect-go` for the runtime
- **North-south API:** Apollo Router (GraphQL gateway), federating service-level GraphQL subgraphs
- **Async messaging:** Confluent Kafka (managed), with Avro schemas in Confluent Schema Registry
- **Data:** Postgres-per-service on Aurora Postgres 15, no shared databases, no cross-service joins
- **Observability:** OpenTelemetry SDKs in every service, exporting to Datadog APM and a self-hosted Tempo + Loki stack for cost control
- **Service mesh:** Istio (ambient mode) for mTLS, retries, and traffic shifting
- **CI/CD:** GitHub Actions for build/test, Argo CD for GitOps deploys, Argo Rollouts for canary

### 3.2 The Twelve Services

Each service owns its data, its deploy cadence, and its on-call rotation. The boundaries below are the result of a 6-week event-storming exercise with product and engineering leads.

1. **AccountService** — Tenant lifecycle, organization hierarchy, user-to-account membership. Owns the `accounts`, `organizations`, `memberships` tables. Source of truth for tenant identity.
2. **IdentityService** — Authentication, session management, SSO/SAML/SCIM, API token issuance. Owns `users`, `credentials`, `sessions`, `api_tokens`.
3. **BillingService** — Subscription management, invoicing, proration, dunning. Owns `subscriptions`, `invoices`, `line_items`, `payment_methods`. Integrates with Stripe.
4. **EntitlementService** — Plan-to-feature mapping, usage limits, feature flag evaluation for paid features. Owns `plans`, `entitlements`, `usage_counters`.
5. **NotificationService** — Transactional email, in-app notifications, webhook delivery to customer endpoints. Owns `notification_templates`, `delivery_log`, `webhook_endpoints`.
6. **IntegrationsService** — Customer-facing integrations (Salesforce, HubSpot, Slack, Zapier). Owns `integration_configs`, `oauth_tokens`, `sync_state`.
7. **ReportingService** — Customer-facing dashboards and exports. Read-only against an analytical replica + materialized rollups. Owns `report_definitions`, `scheduled_exports`.
8. **AuditService** — Append-only audit log for compliance (SOC 2, ISO 27001). Owns `audit_events`. Write-heavy, read-rare.
9. **DocumentService** — Customer file uploads, document generation (PDF invoices, contracts). Owns `documents`, `document_versions`. Backed by S3.
10. **WorkflowService** — Customer-defined automation rules ("when X, then Y"). Owns `workflow_definitions`, `workflow_runs`. The most complex new service; replaces a particularly gnarly corner of the monolith.
11. **AdminService** — Internal employee tooling (impersonation, manual refunds, support overrides). Owns `admin_actions`. Strict RBAC, separate auth path.
12. **GatewayService** — Apollo Router deployment + a thin BFF layer for legacy REST clients we cannot migrate to GraphQL within the program window.

A thirteenth candidate, **AnalyticsService**, is intentionally **deferred** (see Section 12, Open Questions).

---

## 4. Migration Phases

We follow the Strangler Fig pattern: route traffic through a facade, extract one service at a time, validate via shadow traffic, cut over, and decommission the corresponding monolith code path. No "freeze the monolith" period; Big Hank continues to receive feature work throughout.

### Phase 0 — Foundations (Months 1-2)

- Stand up EKS clusters, Argo CD, Istio, Kafka, OTel collectors
- Build the Apollo Router gateway in front of the existing monolith with zero subgraphs (transparent passthrough)
- Establish the "extraction template" — a cookiecutter Go service repo with health checks, gRPC scaffolding, OTel wiring, and a Helm chart
- Migrate CI from CircleCI to GitHub Actions; instrument flake detection
- **Exit criteria:** Gateway serves 100% of prod traffic with <2ms added p99 latency. One "hello world" Go service deployed end-to-end through Argo to prod.

### Phase 1 — Edge Extractions (Months 3-5)

Extract the services with the lowest coupling and clearest boundaries first. These are read-heavy or write-isolated and have minimal cross-domain joins:

- **NotificationService** (Month 3)
- **DocumentService** (Month 4)
- **AuditService** (Month 4-5)
- **IntegrationsService** (Month 5)

Pattern per service: dual-write from monolith to new service for 2 weeks, shadow reads for 1 week, cut over reads, cut over writes, decommission monolith code path.

**Exit criteria:** Four services in production, each deploying at least 1x/day. Monolith Sidekiq worker count reduced by ~30%.

### Phase 2 — Core Domain Extractions (Months 6-8)

The harder middle. These services have meaningful cross-domain coupling and require careful schema work:

- **IdentityService** (Month 6) — High-stakes; auth bugs are SEV-1.
- **AccountService** (Month 6-7) — The "spine" service; everyone depends on it. Read replica fan-out is mandatory.
- **BillingService** (Month 7-8) — Highest blast radius. Dual-write for 4 weeks minimum. Reconciliation job runs nightly comparing monolith vs. service invoices for the full overlap period.
- **EntitlementService** (Month 8) — Extracted alongside Billing; they share several boundary concepts.

**Exit criteria:** Eight services in production. The monolith no longer owns the `accounts`, `users`, `subscriptions`, or `invoices` tables — it reads them via gRPC.

### Phase 3 — Long Tail and Decommission (Month 9)

- **ReportingService**, **WorkflowService**, **AdminService** extracted
- Monolith reduced to a "residual" responsible only for the admin UI shell and a handful of deprecated REST endpoints scheduled for sunset
- AnalyticsService decision made (see Open Questions)
- Run the post-migration retrospective; write the public engineering blog post

**Exit criteria:** Big Hank's RDS instance downsized from 16xlarge to 4xlarge. All twelve services owning their own data. Deploy cadence target hit (see Section 11).

---

## 5. Functional Requirements

> **Note to ARB reviewers:** This section is intentionally prescriptive about implementation. Per the staff-engineer-RFC convention, decisions made at this layer are binding on extraction teams unless overridden via a follow-up RFC.

**FR-1 — gRPC Schema Management.** All service-to-service contracts MUST be defined in `.proto` files stored in the `helios-protos` monorepo. Schemas MUST be linted with `buf lint` using the `DEFAULT` ruleset plus `RPC_REQUEST_STANDARD_NAME` and `RPC_RESPONSE_STANDARD_NAME`. Breaking changes MUST be detected via `buf breaking` against the `main` branch and require explicit RFC approval. Generated Go code MUST use `connect-go` v1.16+, not the legacy `grpc-go` stack.

**FR-2 — GraphQL Federation.** The Apollo Router gateway MUST run federation v2. Each service exposing GraphQL MUST publish a subgraph schema with `@key` directives on all root types. Subgraph composition MUST be validated in CI using `rover subgraph check` against the production supergraph before any subgraph deploy is allowed to proceed.

**FR-3 — Postgres-per-Service.** No service MAY share a database with another service. No service MAY perform a cross-database join. Cross-service data access MUST go through gRPC. Each service's Postgres instance MUST be Aurora Postgres 15.4 or later, with `rds.force_ssl=1` and IAM authentication enabled for the application role.

**FR-4 — Database Migrations.** All schema migrations MUST use `golang-migrate/migrate` v4 with up/down files versioned by Unix timestamp. Migrations MUST be applied via an init container in the service Deployment, NOT via a separate Job. Backward-incompatible migrations (column drops, type narrowing) MUST follow the expand-migrate-contract pattern across at least two deploys.

**FR-5 — Kafka Event Contracts.** All domain events MUST be published to Kafka topics named `helios.<service>.<aggregate>.<event>` (e.g., `helios.billing.invoice.finalized`). Payloads MUST be Avro-encoded with schemas registered in Confluent Schema Registry. Compatibility mode MUST be set to `BACKWARD` on all topics. Event envelopes MUST include `event_id` (ULID), `occurred_at` (RFC3339 nanos), `tenant_id`, and `trace_id` headers.

**FR-6 — Idempotency.** All mutating gRPC endpoints MUST accept an `idempotency_key` field in the request metadata. Services MUST persist idempotency keys in a `idempotency_keys` table with a 24-hour TTL and return the cached response on replay. Kafka consumers MUST be idempotent at the aggregate level, not relying on Kafka's exactly-once semantics.

**FR-7 — Authentication Between Services.** Service-to-service calls MUST authenticate via mTLS provided by Istio. Application-level authorization MUST use a propagated `tenant_context` header containing a signed JWT issued by IdentityService, validated at each hop via JWKS. The shared service-account-bearer-token pattern is explicitly prohibited.

**FR-8 — Outbox Pattern for Dual-Writes.** Services that publish Kafka events as a side effect of a database write MUST use the transactional outbox pattern. The outbox table MUST be named `event_outbox` with columns `id`, `aggregate_id`, `event_type`, `payload`, `created_at`, `published_at`. A `debezium-connector` running on Kafka Connect MUST tail the outbox via logical replication slots.

**FR-9 — Health Checks.** Every service MUST expose `/healthz` (liveness) and `/readyz` (readiness) HTTP endpoints on port 8081, separate from the gRPC port (9090). Readiness MUST check database connectivity, Kafka producer health, and any required downstream gRPC dependencies. Liveness MUST NOT check downstream dependencies.

**FR-10 — Structured Logging.** All services MUST emit JSON-structured logs via `log/slog` (stdlib, Go 1.21+). Logs MUST include `trace_id`, `span_id`, `tenant_id`, `service`, `version`, and `severity` fields. Log lines MUST NOT exceed 32 KiB. PII fields MUST be redacted at emission via the `helios-redact` library.

**FR-11 — Distributed Tracing.** All inbound and outbound calls (gRPC, HTTP, Kafka, Postgres) MUST be instrumented via the OpenTelemetry Go SDK with the `otelgrpc`, `otelhttp`, `otelsql`, and `otelkafka` packages. Sampling MUST be tail-based, configured at the OTel Collector with a 100% retention policy for traces containing errors or spans >500ms.

**FR-12 — Feature Flags.** Runtime flag evaluation MUST be performed against an OpenFeature provider backed by LaunchDarkly. The legacy `flags` table in the monolith MUST NOT be replicated into new services. Flag keys MUST follow `helios.<service>.<feature>.<variant>` naming. Flag SDKs MUST be initialized at process start with a 30-second timeout and a documented fallback default per flag.

**FR-13 — Rate Limiting.** Public-facing endpoints exposed via the gateway MUST be rate-limited at Apollo Router using the `@rateLimit` directive, with limits keyed on `tenant_id` first and `user_id` second. Internal gRPC endpoints MUST enforce per-tenant concurrency limits via a token-bucket interceptor (`helios-throttle` library).

**FR-14 — Pagination.** All list endpoints MUST use cursor-based pagination with opaque base64-encoded cursors. Offset/limit pagination is prohibited for any list expected to exceed 1,000 rows. Cursors MUST be stable across schema changes (i.e., include enough fields to disambiguate composite sort orders).

**FR-15 — Backfill and Reconciliation.** Every service extraction MUST include (a) a backfill job that copies historical data from the monolith Postgres to the service Postgres, (b) a reconciliation job that runs nightly during the overlap period and reports row-level diffs to a Slack channel, and (c) a documented rollback runbook validated in staging by a non-author engineer.

---

## 6. Non-Functional Requirements

| ID | Category | Requirement | Target | Measurement |
|----|----------|-------------|--------|-------------|
| NFR-1 | Latency | gRPC p50 server-side | < 8 ms | Datadog APM |
| NFR-2 | Latency | gRPC p99 server-side | < 80 ms | Datadog APM |
| NFR-3 | Latency | GraphQL gateway p99 end-to-end | < 250 ms | Datadog RUM + APM |
| NFR-4 | Latency | Kafka consumer lag (p99) | < 5 s | Kafka Lag Exporter |
| NFR-5 | Availability | Per-service SLO | 99.95% monthly | SLO burn-rate alerts |
| NFR-6 | Availability | Gateway SLO | 99.97% monthly | Synthetic probes |
| NFR-7 | Deploy | Per-service deploy frequency | >= 3/day median | Argo CD metrics |
| NFR-8 | Deploy | Lead time PR-merge to prod | < 30 min p50 | GitHub + Argo |
| NFR-9 | Deploy | Rollback time | < 5 min | Argo Rollouts |
| NFR-10 | Deploy | Canary auto-abort window | < 2 min on SLO burn | Argo Rollouts + Datadog |
| NFR-11 | Observability | Trace coverage | 100% of RPC paths | OTel collector audit |
| NFR-12 | Observability | Log-to-trace correlation | 100% | Grep `trace_id` in 1000 sampled logs |
| NFR-13 | Observability | Dashboard freshness | < 60 s | Datadog ingestion lag |
| NFR-14 | Security | mTLS coverage between services | 100% | Istio policy audit |
| NFR-15 | Security | Secrets rotation cadence | <= 90 days | AWS Secrets Manager |
| NFR-16 | Data | RPO per service | < 5 min | Aurora continuous backup |
| NFR-17 | Data | RTO per service | < 30 min | DR game-day twice/year |
| NFR-18 | Cost | Monthly AWS spend at month-9 | <= $200k | AWS Cost Explorer |
| NFR-19 | CI | PR feedback time p50 | < 8 min | GitHub Actions metrics |
| NFR-20 | CI | Test flake rate | < 0.3% | Custom flake detector |

---

## 7. Risk Register

| # | Risk | Likelihood | Impact | Mitigation |
|---|------|------------|--------|------------|
| R-1 | Billing dual-write produces silent invoice divergence during overlap | Medium | Critical | Nightly reconciliation job; finance sign-off gate before cutover; 4-week minimum overlap; finance ops paged on any diff > $0.01 |
| R-2 | Kafka becomes a new SPOF; an outage takes down all event flows | Low | Critical | Confluent multi-AZ cluster; circuit breakers in producers fall back to outbox-only mode; runbook for "degrade to sync" path |
| R-3 | gRPC schema breakage between services deploys out of order | Medium | High | `buf breaking` in CI; required deploy ordering encoded in Argo App-of-Apps; consumer-driven contract tests via Pact |
| R-4 | AccountService extraction blocks every other service and slips | High | High | Start AccountService schema work in Phase 1 even though cutover is Phase 2; dedicate two senior engineers full-time; weekly ARB checkpoint |
| R-5 | Team attrition mid-migration (we are already 2.3x burnout on on-call) | Medium | High | Aurora team gets new dedicated on-call rotation separate from product; hiring backfill plan approved before kickoff; clear comp/title impact for migration leads |
| R-6 | Postgres-per-service explodes operational surface area for SRE | High | Medium | Aurora Postgres + RDS Proxy standardization; one Terraform module for all service DBs; SRE oncall headcount +2 |
| R-7 | Apollo Router licensing / GraphOS cost balloons with traffic | Low | Medium | Negotiate enterprise contract in Phase 0; have self-hosted Router fallback validated; cost-per-million-requests tracked weekly |
| R-8 | Customers depending on undocumented monolith behavior (response shape quirks, race conditions) hit by cutover | Medium | High | Shadow-traffic comparison for 1 week per service; opt-in beta cohort for top 50 accounts; rollback flag per endpoint |

---

## 8. Migration Milestones

**Q3 2026 (Jul-Sep) — Foundations & Edge**
- Jul 15: EKS prod cluster GA; Argo CD operational
- Aug 1: Apollo Router in front of monolith at 100% traffic
- Aug 22: NotificationService cutover complete
- Sep 19: DocumentService cutover complete
- Sep 30: AuditService cutover complete; Phase 1 retrospective

**Q4 2026 (Oct-Dec) — Core Domain**
- Oct 17: IntegrationsService cutover complete (closes Phase 1)
- Nov 7: IdentityService cutover complete
- Nov 28: AccountService cutover complete (the spine)
- Dec 19: BillingService dual-write begins (4-week overlap into Q1)

**Q1 2027 (Jan-Mar) — Long Tail & Wrap**
- Jan 16: BillingService cutover complete
- Jan 30: EntitlementService cutover complete (closes Phase 2)
- Feb 13: ReportingService cutover complete
- Feb 27: WorkflowService cutover complete
- Mar 13: AdminService cutover complete; residual monolith locked
- Mar 27: RDS downsizing complete; final retrospective; public blog post

---

## 9. Non-Goals

- **We are not rewriting in Rust.** Rust is a fine language. The talent pool inside Helios skews toward Go and Ruby. The marginal performance gain is not worth the hiring and training cost over a 9-month window.
- **We are not changing the frontend stack.** The React/TypeScript SPA stays. GraphQL contracts change underneath it; consumer experience does not.
- **We are not adopting Kubernetes operators for stateful workloads.** Postgres stays on managed Aurora. Kafka stays on managed Confluent. We will not run a Postgres operator on EKS during this program.
- **We are not migrating to a different cloud.** Aurora is an AWS-native program. Multi-cloud and cloud portability are explicit non-goals.
- **We are not building a new mobile API.** The existing REST endpoints used by the iOS app continue to be served from the gateway BFF layer. A native mobile-GraphQL migration is a separate future RFC.
- **We are not rewriting the admin UI.** AdminService gets a new backend; the existing Rails-rendered admin views are wrapped, not replaced, within this program.
- **We are not adopting a service mesh other than Istio.** Linkerd and Cilium were evaluated; Istio ambient mode wins on our team's existing operational knowledge.
- **We are not aiming for "zero downtime" on every service.** Auth (IdentityService) cutover requires a brief (target: <90s) read-only window. This is acceptable and pre-communicated.

---

## 10. Success Metrics

Measured at month 9 (end of program) against the month 0 baseline:

| Metric | Baseline (May 2026) | Target (Mar 2027) | Stretch |
|--------|---------------------|-------------------|---------|
| Deploy frequency (median per service) | 0.14/day (1x/week monolith) | 3/day | 8/day |
| Lead time, PR-merge to prod (p50) | 9.4 days | 30 min | 15 min |
| Change failure rate | 14% (rollbacks + hotfixes) | < 8% | < 5% |
| MTTR for SEV-2+ | 2h 18min | 45 min | 25 min |
| Gateway p99 latency | 410 ms | 250 ms | 180 ms |
| Full CI feedback time (p50) | 47 min | 8 min | 5 min |
| Test flake rate | 2.1% | 0.3% | < 0.1% |
| Monthly AWS spend | $340k | $200k | $180k |
| On-call pages per week (org-wide) | 38 | 18 | 12 |
| On-call attrition rate | 2.3x baseline | 1.0x baseline | < 1.0x |

A program is not a success unless **at least 7 of 10** of these targets are hit, AND deploy frequency, lead time, MTTR, and AWS spend are all hit. We will not declare victory on observability gains alone.

---

## 11. Staffing and Org

- **Aurora Platform team (new):** 6 engineers (1 TL, 5 senior/staff), 1 EM, 1 dedicated SRE
- **Embedded migration leads:** 1 senior engineer per product squad (7 squads), part-time allocation 40-60%
- **Architecture Review Board:** Existing ARB reviews each per-service RFC; this document is the parent
- **Finance partner:** A dedicated finance ops contact for Billing reconciliation work
- **Security partner:** A dedicated AppSec engineer reviews IdentityService and AdminService extractions

Total estimated effort: ~32 engineer-years over 9 calendar months.

---

## 12. Open Questions

These are unresolved decisions that the ARB is asked to either resolve in-meeting or assign owners for resolution within 30 days of program approval.

**OQ-1 — AnalyticsService.** The customer-facing analytics surface (dashboards, exports, cohort reports) currently lives in a corner of the monolith that joins across `accounts`, `subscriptions`, `usage_events`, and `invoices`. Three options on the table:
- (a) Extract as a thirteenth service in Phase 3
- (b) Defer to a follow-up program and let ReportingService cover the basics
- (c) Replace with a vendor (Metabase, Cube, or a build on top of ClickHouse)

The author's recommendation is (b), defer. The ARB should confirm or override.

**OQ-2 — Sidekiq replacement strategy.** As we extract services, the monolith's Sidekiq jobs need a destination. Two camps internally: (1) port jobs one-by-one to per-service Kafka consumers; (2) stand up a shared "JobService" that proxies legacy Sidekiq semantics during transition. Camp 1 is purer; camp 2 is faster. No decision yet.

**OQ-3 — Multi-region.** Helios is currently single-region (us-east-1). EU customers have started asking. Is Aurora the right program to introduce multi-region, or is that a follow-up? Current author position: follow-up, but the per-service architecture must not foreclose multi-region (no globally-unique-integer IDs, no region-coupled assumptions in code).

**OQ-4 — GraphQL vs. gRPC-Web for the SPA.** Apollo Router via GraphQL is the plan. But several engineers have advocated for gRPC-Web directly from the SPA, bypassing GraphQL. Decision needed before Phase 1 cutover of any service whose data the SPA reads.

**OQ-5 — Test environment strategy.** The current single shared staging environment is contention-bound. Options: per-service ephemeral envs (vcluster), shared "preview" envs per PR (Telepresence), or invest in better staging data hygiene. No clear winner.

**OQ-6 — Datadog cost ceiling.** At current trajectory, Datadog spend will grow from $52k/month to ~$95k/month under the new architecture due to span volume. Self-hosting Tempo + Loki is in the plan, but the cutover point is undecided. Need a cost-tipping-point analysis.

**OQ-7 — Residual monolith fate.** After Phase 3, ~80k LOC of monolith remains. Do we (a) maintain it indefinitely as "the admin shell + deprecated REST", (b) put it on a deprecation clock with a hard sunset date, or (c) keep extracting in a follow-up program? Author recommendation: (b), 12-month sunset clock starting Mar 2027.

---

## 13. Appendix A — Why Go (Brief)

A full language-evaluation memo is linked separately. Summary:
- **Go vs. Ruby:** Ruby is fine for product code but the operational characteristics (GC pauses, memory footprint, container density) are no longer cost-competitive.
- **Go vs. Kotlin:** Kotlin/JVM was the runner-up. We rejected on the basis of (1) JVM tuning operational cost, (2) existing Go expertise in the Platform team, (3) startup time matters for Lambda-edge use cases that may emerge later.
- **Go vs. Rust:** Rust was rejected for hiring-pool reasons and because none of our identified bottlenecks are CPU-bound to a degree that Rust would meaningfully outperform Go.
- **Go vs. TypeScript/Node:** Rejected on the basis of unsafe defaults, dependency-tree risk, and weaker single-process concurrency story for our workload shape.

---

## 14. Appendix B — Glossary

- **Strangler Fig:** Martin Fowler's pattern for incrementally replacing a legacy system by routing traffic through a facade and migrating one capability at a time.
- **Bounded Context:** From DDD; a logical boundary within which a domain model is consistent.
- **Outbox Pattern:** A transactional pattern where domain events are written to a database table in the same transaction as the state change, then asynchronously published.
- **Expand-Migrate-Contract:** A schema-change pattern: first add the new shape (expand), then move data and traffic (migrate), then remove the old shape (contract).
- **Big Hank:** The Rails monolith, currently 600k LOC. Named affectionately and despairingly.

---

**End of document.**

*Reviewers: please leave comments inline. ARB meeting scheduled for 2026-05-29.*
