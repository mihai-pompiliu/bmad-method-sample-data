# PRD: Skyharbor (Fork of Skyworks)

Authors: Engineering Director, Open Source Lead
Status: Draft for internal RFC, pre-announcement
Date: 2026-05-17

## 1. Project Context

Skyworks is a popular open-source workflow orchestrator (Apache 2.0), widely deployed for data and operational pipelines. Our team has built on Skyworks for three years. It runs the core scheduling layer for our production product and for several of our paying customers' internal platforms.

Skyworks v3 shipped 14 months ago. Since then the project has entered what we will diplomatically call major-release stagnation. The RFC pipeline has slowed. Governance is concentrated in a single corporate sponsor whose roadmap priorities have diverged from the broader contributor base. We have been good-faith contributors, not drive-by ones. That context matters for what follows.

We are proposing to fork Skyworks into a new project, working name **Skyharbor**. This document specifies what the fork is, what it is not, and how we intend to behave toward upstream and the community.

## 2. Upstream Relationship

We have shipped non-trivial code into Skyworks for three years. Over the last 18 months, three of our six substantive PRs have stalled:

- **BYO-secrets-store integrations** (PR open 14 months, last maintainer comment 9 months ago)
- **Async-native task graph compilation** (PR open 11 months, blocked on an RFC that has not been scheduled)
- **Multi-tenant scheduler** (PR open 6 months, marked "needs design discussion," no discussion has been held)

The other three PRs (a logging fix, a Kubernetes executor bug, and a docs cleanup) merged within normal cycles. The pattern is consistent: incremental fixes move, architecturally significant changes do not.

We have raised the pipeline issue privately with upstream maintainers twice and on the public mailing list once. The responses have been cordial and have not produced movement. Our customers have begun to ask, reasonably, when the features they have been told are coming will arrive.

A fork is the option of last resort, not first. We are choosing it because the alternative is maintaining an ever-growing private patch set with no path to convergence.

## 3. License and Trademark Posture

**License.** Upstream is Apache 2.0. Skyharbor will remain Apache 2.0. We will not relicense. No BSL. No SSPL. No source-available games. If we ever need to revisit this, it will be through a public RFC with a 90-day comment window, and the default answer is no.

**Trademark.** "Skyworks" is a registered trademark of the upstream sponsor. We will not use it. The fork name (Skyharbor) signals lineage without overlap. We will not use upstream's logo, wordmark, or visual identity. Documentation will state plainly: "Skyharbor is a fork of the Skyworks project. Skyworks is a trademark of its respective owner."

**Attribution.** Every source file we inherit retains its original copyright headers. NOTICE and LICENSE files are preserved verbatim. Our additions are co-attributed.

## 4. Fork Goals

1. Ship the three capabilities upstream cannot or will not ship on a timeline our users can plan around.
2. Maintain plugin and config compatibility with Skyworks so the migration cost for existing users is near zero.
3. Track upstream changes continuously so users do not lose access to upstream improvements.
4. Establish independent governance with a transparent, time-bound RFC process.
5. Stand up community infrastructure (website, docs, chat, issue tracker) that does not depend on upstream's resources or goodwill.

## 5. Where We Diverge (Capability Deltas)

These are the real reasons the fork exists. Everything else is plumbing.

### 5.1 Pluggable Scheduler Architecture

Upstream's scheduler is monolithic. The scheduling loop, the executor dispatch, and the persistence layer are interleaved across modules with no stable seam. We will refactor to a scheduler interface with a reference implementation that matches current upstream behavior, plus a second implementation optimized for multi-tenant workloads. Third-party schedulers become possible.

### 5.2 Multi-Tenant Isolation

Namespace boundaries, per-namespace RBAC, per-namespace resource quotas, and tenant-scoped audit logging. Upstream's model assumes a single trust boundary. Ours does not. This is the feature our paying customers have been waiting on for two years.

### 5.3 Async-Native Task Graph Compilation

The graph compiler currently materializes everything synchronously at parse time. For large DAGs and dynamic task generation this is the dominant latency cost in our deployments. The async-native compiler is the work that has been sitting in upstream PR review for 11 months. We will ship it.

## 6. Where We Keep Upstream Parity

We are forking the project, not the user experience. Concretely:

- **CLI surface.** All `skyworks` commands are mirrored as `skyharbor` commands with identical flags, identical exit codes, and identical output shapes. An alias mode lets users invoke `skyworks` against Skyharbor binaries during transition.
- **Config and DAG file format.** Pipeline definitions written for Skyworks parse and execute under Skyharbor without modification. New capabilities are exposed through additive fields with safe defaults.
- **Plugin ABI.** Existing upstream plugins must continue to work for at least the first two minor versions of Skyharbor (v1.0, v1.1). Plugin authors get advance notice and a migration path before any break.
- **Metrics and logs.** Metric names, log formats, and observability hooks remain stable.

## 7. Upstream Merge Cadence

We are a fork. We are not a rewrite. We commit to:

- **Weekly tracking.** A scheduled job pulls upstream commits and opens a tracking issue summarizing what landed.
- **Monthly bulk merge.** Upstream commits are merged into Skyharbor on a monthly cadence, or sooner if a security fix lands.
- **Backlog target.** The upstream-merge backlog (measured as days between an upstream commit landing and that commit being present in Skyharbor main) stays under 30 days. We will publish this metric monthly.
- **Conflict policy.** Where upstream and Skyharbor diverge on the same code, Skyharbor's version wins, but the diff is documented in a `DIVERGENCE.md` ledger so contributors and downstream users can see exactly what differs and why.

## 8. Naming, Branding, Repository Governance

- **Name.** Skyharbor. Distinct phonetics, distinct visual identity, lineage acknowledged in docs.
- **Repository.** `github.com/<our-org>/skyharbor`. Public from day one.
- **Issue tracker.** GitHub Issues. No private triage.
- **Chat.** A new Discord. We will not squat on upstream's community channels or recruit from them.
- **Website and docs.** `skyharbor.dev` (placeholder), independent hosting, independent search index, independent contributor guide.
- **Governance model.** Corporate-sponsored open source. We will say that out loud. The maintainer team is currently five engineers from our company plus two external maintainers who have agreed to join. We will publish a governance document covering maintainer nomination, RFC process (90-day comment window for non-backport changes), and a path for the project to add external maintainers and eventually move to a foundation if the community grows to warrant it.

## 9. Maintenance Commitments

- Security patch SLA: 7 days for critical, 30 days for high, 90 days for medium.
- LTS line: every other minor version is designated LTS with 18 months of security support.
- Public CI: all tests run on public infrastructure. No private CI gating merges.
- Release cadence: minor releases every 8 weeks, patch releases as needed.

## 10. Compatibility Matrix (v1.0)

| Surface | Compatible with Skyworks v3 | Notes |
| --- | --- | --- |
| CLI flags | Yes | Identical surface, plus additive flags for new features |
| DAG / config file format | Yes | Additive fields only |
| Plugin ABI | Yes (through v1.1) | Deprecations announced one minor version in advance |
| Metrics names | Yes | Additive only |
| REST API | Yes | Versioned endpoints, additive only |
| Database schema | Yes (forward migration provided) | Backward migration documented but not automated |

## 11. Non-Goals

Stated explicitly so we can refuse scope expansion later:

- **Not relicensing.** Apache 2.0 stays.
- **Not changing the DAG/config file format.** Additive only.
- **Not breaking the CLI.** Additive only.
- **Not building a managed cloud service in v1.** A hosted Skyharbor is a plausible monetization path, but it is explicitly out of scope for v1 and will be proposed separately if and when it makes sense.
- **Not diverging for the sake of divergence.** We change behavior where we have an articulated reason. We do not change behavior to signal that we are a different project.
- **Not pursuing trademark disputes with upstream.** If upstream raises a concern about our naming, branding, or positioning, we will address it directly and in good faith.
- **Not recruiting from upstream's maintainer pool.** Contributors who choose to participate in both projects are welcome. We will not solicit.

## 12. Risks

- **Community split.** A fork divides attention. Users may pick wrong, contributors may pick wrong, and the ecosystem absorbs the cost. Mitigation: maximize compatibility, minimize ideological framing, keep upstream-merge backlog short.
- **Upstream backlash.** Maintainers may interpret the fork as an attack. Mitigation: brief upstream maintainers privately before public announcement, offer to continue upstreaming non-divergent fixes, do not engage in public rhetoric.
- **Capture perception.** A corporate-sponsored fork looks like a power play even when it is not. Mitigation: transparent governance document, external maintainers from day one, public RFC process, no relicensing.
- **Maintenance burden underestimate.** Tracking upstream while shipping new capabilities is more expensive than either alone. Mitigation: dedicated headcount for upstream merge work, not a side responsibility.
- **Feature parity regression.** If we drift on upstream parity faster than we intend, users get stranded. Mitigation: the compatibility matrix is part of every release checklist, not an afterthought.

## 13. Success Signals (12-Month Horizon)

- 25 or more organizations running Skyharbor in production by month 12 (self-reported through an adopters file or survey).
- 15 or more contributors who were not previously upstream contributors, with at least 5 of them citing stalled upstream PRs as their reason for joining.
- Upstream-merge backlog median under 30 days, published monthly.
- Zero security incidents traceable to divergence between Skyharbor and upstream.
- A governance document ratified, published, and amended at least once through the public RFC process (proof that the process functions).

## 14. Open Questions

- Do we announce on a fixed date, or after the v1.0 release candidate is cut? Leaning toward the latter so the first impression is "working software" not "press release."
- Foundation path: which foundation, on what timeline, gated on what adoption thresholds? Deferred to a separate RFC.
- Do we offer a paid support tier on day one or wait? Probably wait. The fork's credibility depends on it not looking like a sales funnel.

## 15. Decision Requested

Approval to:

1. Stand up the Skyharbor repository, website, and community infrastructure.
2. Brief upstream maintainers privately, two weeks before public announcement.
3. Ship a v1.0 release candidate within 90 days, containing the three divergent capabilities and the upstream-parity compatibility surface described in sections 5 and 6.
4. Commit the headcount described in section 9 to ongoing maintenance and upstream tracking.
