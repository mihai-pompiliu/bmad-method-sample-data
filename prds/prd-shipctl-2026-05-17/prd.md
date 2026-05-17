# PRD: shipctl

Owner: Platform Tools
Status: Draft
Last updated: 2026-05-17

## Background / Why

`deploy.sh` started life as a 40-line helper for pushing a single service to our staging cluster. Three years later it is 2,047 lines of Bash, forked four times across Payments, Search, Growth, and Infra, each fork with its own undocumented flags (`--yolo`, `--skip-canary-i-know-what-im-doing`, `--region=all-but-eu`). Nobody can describe the rollback behavior without grepping the script, and in practice there is no rollback discipline at all: when a deploy goes bad at 2am, on-call copies a kubectl command out of a runbook and hopes. We need a single, opinionated CLI that encodes how Reef deploys are supposed to work, kills the forks, and gives the platform team a chance at observability over what 300 engineers are actually doing in production.

## Goals

- Replace `deploy.sh` (and its forks) with one binary that ships platform best practices by default: canary, regional staggering, automatic SLO-driven rollback.
- Make the safe path the easy path. `shipctl deploy` with no flags should do the right thing for 90% of services.
- Give platform engineers real telemetry on how deploys are actually being run, so we can find the rough edges instead of guessing.
- Cut "first successful deploy" time for a new engineer from a day (current state: read the wiki, ask in Slack, get a working `deploy.sh` invocation) to under 15 minutes.

## Users

- **Platform engineers** (us, ~12 people): own Reef, own shipctl, want telemetry and a smaller blast radius from bad deploys.
- **Application engineers** (~280 people): want to ship their service without learning Kubernetes. Primary daily user. Lives in `shipctl deploy` and `shipctl status`.
- **On-call responders** (rotating, ~80 people in the pool): wake up at 2am, need `shipctl rollback` and `shipctl status` to be obvious and fast. Will not read docs in this state.

## Command Surface

```
shipctl deploy [SERVICE]              Deploy current repo's service. Reads .shipctl.yaml.
  --version <sha|tag>                 Override image version (default: current git SHA)
  --regions <list|all>                Target regions (default: all configured)
  --canary <pct>                      Canary percentage (default: 5)
  --skip-canary                       Skip canary stage (requires --reason)
  --reason <text>                     Required for any safety override
  --dry-run                           Print plan, do nothing
  --wait / --no-wait                  Block until healthy (default: wait)

shipctl rollback [SERVICE]            Roll back to previous known-good revision.
  --to <revision>                     Pin a specific revision
  --regions <list>                    Limit rollback scope
  --reason <text>                     Required

shipctl status [SERVICE]              Show current rollout state across regions.
  --watch                             Stream updates
  --json                              Machine-readable output

shipctl canary promote [SERVICE]      Promote current canary to 100%.
  --skip-soak                         Skip remaining soak window (requires --reason)

shipctl canary abort [SERVICE]        Abort canary, return to previous stable.

shipctl history [SERVICE]             List recent deploys with outcome + duration.
  --limit <n>                         Default 20

shipctl diff [SERVICE]                Show diff between current stable and pending deploy.

shipctl logs [SERVICE]                Tail logs from the active rollout's pods.
  --region <name>                     Filter by region
  --since <duration>                  Default 5m

shipctl config validate               Validate .shipctl.yaml against org defaults.

shipctl whoami                        Print current identity, token expiry, org defaults in effect.
```

## Functional Requirements

- **FR-1** `shipctl deploy` with no flags MUST execute: build manifest → canary 5% in region 1 → soak → expand to 100% in region 1 → repeat for subsequent regions in configured order.
- **FR-2** Regional staggering order MUST be configurable per service but default to the org-level order defined by the platform team.
- **FR-3** During canary and during each regional expansion, shipctl MUST poll the Reef SLO API and auto-rollback if any tracked SLO breaches its rollback threshold.
- **FR-4** Auto-rollback MUST complete within 90 seconds of breach detection and MUST emit a structured event to the platform telemetry endpoint.
- **FR-5** Any flag that bypasses safety (`--skip-canary`, `--skip-soak`, etc.) MUST require `--reason` with non-empty text. Reasons are logged and surfaced in `shipctl history`.
- **FR-6** `shipctl rollback` MUST work without network access to the repo (no git fetch required); it relies on Reef's revision history.
- **FR-7** `shipctl status` MUST render a per-region table showing: current revision, target revision, canary %, SLO status, time-in-state.
- **FR-8** `shipctl status --json` MUST emit a stable, versioned JSON schema for CI consumption.
- **FR-9** `shipctl diff` MUST show: image SHA delta, config delta, and any changed resource limits, in that order.
- **FR-10** `shipctl deploy --dry-run` MUST produce the same plan output that a real deploy would execute, with no side effects and no auth required beyond identity.
- **FR-11** All commands MUST honor `--json` for machine-readable output; absent the flag, output is human-formatted with color when stdout is a TTY.
- **FR-12** shipctl MUST refuse to operate if the local `.shipctl.yaml` fails `shipctl config validate`, with an actionable error.

## Error Model

shipctl distinguishes between user errors, platform errors, and policy refusals. Each maps to a stable exit code:

- `0` success
- `1` generic failure (reserved; we try not to use it)
- `2` usage error (bad flag, missing required arg)
- `3` config error (`.shipctl.yaml` invalid or missing)
- `4` auth error (no token, expired token, insufficient scope)
- `5` policy refusal (org policy blocks this action, e.g. skipping canary in prod without an approved reason code)
- `6` platform error (Reef API unreachable or returned 5xx)
- `7` rollout failed (SLO breach, health check failure, timeout)
- `8` rollback failed (escalate immediately)

Human-readable mode prints a single-line cause, a short explanation, and a hint:

```
Error: rollout failed in region us-west-2 (exit 7)
Cause: latency_p99 exceeded 800ms rollback threshold during canary soak
Hint:  shipctl status checkout-api --region us-west-2
       shipctl logs checkout-api --region us-west-2 --since 10m
```

`--json` mode emits a single JSON object on stderr with `code`, `cause`, `details`, and `hints[]`. CI consumers should parse this; humans should not. The hint system is data-driven: every error class has a list of follow-up commands registered in code, not strings improvised at the call site.

## Telemetry

shipctl sends events to the platform telemetry endpoint after every command invocation (success or failure). Events include:

- Command path (`deploy`, `canary promote`, etc.) and which flags were set, but not flag values that could leak service or environment data beyond service name.
- Exit code and duration.
- Failure class when applicable (SLO breach, auth, policy, platform).
- Client version, OS, terminal vs CI environment.
- For deploys: regions touched, canary percentage used, whether any safety override was invoked and the reason code (not free text).

We do NOT send: stdout, stderr, manifest contents, or env vars. Users can opt out with `SHIPCTL_TELEMETRY=off`, but the default is on; this is a workplace tool, not a public CLI.

The platform team uses this to answer questions like: which commands are most-used, which fail most often, what's the p50/p95 duration of a full deploy, who is `--skip-canary`-ing and why.

## Configuration

Per-repo `.shipctl.yaml` declares the service:

```yaml
service: checkout-api
runtime: jvm
regions: [us-east-1, us-west-2, eu-west-1]
canary:
  percent: 5
  soak: 10m
slos:
  - latency_p99 < 800ms
  - error_rate < 0.5%
owners:
  team: payments
  slack: "#payments-oncall"
```

Org-level defaults live in a platform-owned repo and are fetched at command start (cached locally with a 1h TTL, refresh on `shipctl config validate`). Defaults define: required SLO classes per environment, allowed canary minimums, regional ordering, approved reason codes. Per-repo config can narrow but cannot widen org policy. `shipctl whoami` shows which defaults are currently in effect.

## Non-Functional Requirements

- **Cold start** under 200ms for read-only commands (`status`, `history`, `whoami`) on a warm cache. No JVM, no Python, no Node. Go binary.
- **Distribution** via Homebrew tap (macOS/Linux dev laptops) and an internal apt repo (CI runners). Single static binary, no runtime deps.
- **Offline-capable** for read-only ops: `history`, `status` (against cached state), `config validate`, `diff` (against last-fetched stable) work without network.
- **Auth** via existing SSO. Tokens are short-lived (1h) and refreshed transparently via the existing device-flow helper. No long-lived secrets on disk.
- **Backwards compatibility** for the `--json` schema: minor version bumps additive only, breaking changes require a new schema version field consumers can pin.

## Non-Goals

- shipctl is NOT a general-purpose Kubernetes CLI. It will not grow `shipctl exec`, `shipctl port-forward`, or `shipctl apply`. Use kubectl for those, knowing the platform team will not support arbitrary kubectl usage.
- shipctl is NOT a UI. No web dashboard, no Electron app. The TUI question is open (see below) but is the only UI surface under consideration.
- shipctl does NOT replace the GitOps controller. The controller remains the source of truth for desired state in long-lived environments. shipctl drives rollouts and exposes status; it does not bypass the controller.
- shipctl does NOT support non-Reef clusters. If you're deploying to a vendor cluster, an experimental edge cluster, or your laptop, use the vendor's tooling.

## Success Metrics

- **Adoption**: 90% of production deploys go through shipctl within 6 months of GA. Measured by: shipctl telemetry deploys / total Reef rollout events.
- **Deploy success rate**: shipctl deploys succeed at >= the rate of the old `deploy.sh` baseline (currently ~94% first-attempt success), and ideally clear 97% within a year as canary catches bad deploys earlier.
- **MTTR on rollback events**: median time from SLO breach to stable state under 3 minutes. Old script baseline is ~18 minutes (manual rollback) when rollback happens at all.
- **First-deploy time** for new engineers: under 15 minutes from `brew install shipctl` to a successful staging deploy, measured by a quarterly survey of new hires.
- **Fork count**: zero forks of shipctl. If a team wants behavior we don't support, that's a feature request, not a fork.

## Open Questions

- **Multi-cluster deploys within a region**: some services span two clusters in `us-east-1` for capacity reasons. Do we model that as a deploy primitive, or push it down into Reef's rollout controller and keep shipctl ignorant?
- **TUI for status**: `shipctl status --watch` is fine, but on-call has asked for a fuller status TUI showing all of a team's services at once. Worth doing, or does it pull us into UI work we said we wouldn't do?
- **Reason codes vs free text**: org defaults enumerate approved reason codes for safety overrides, but engineers want free text for nuance. Allow both (code required, free text optional), or force one?
- **CI vs interactive auth**: CI runners use service accounts; humans use SSO. Should `shipctl whoami` make this distinction loud, and should certain commands (e.g. `--skip-canary`) be flat-out forbidden from service-account contexts?
- **Rollback on partial regional failure**: if region 1 succeeds and region 2 fails canary, do we auto-rollback region 1 too, or hold it and alert? Current bias: hold and alert, but we should validate with on-call.
