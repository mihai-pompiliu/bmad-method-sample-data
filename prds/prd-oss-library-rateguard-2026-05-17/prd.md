# RateGuard PRD

**Status:** Draft / RFC (pre v0.1)
**Owner:** Maintainer
**Date:** 2026-05-17
**Package:** `rateguard`
**License:** MIT

> Library: TypeScript / Node.js, targeting Node 20+, Bun 1.0+, Deno 1.40+, and modern evergreen browsers. Zero runtime dependencies. ESM-first with CJS interop.

---

## 1. Background and Motivation

Every non-trivial application that talks to a third-party HTTP API eventually hits a rate limit. The current state of the ecosystem is:

- `p-queue`, `bottleneck`, `limiter`: client-side throttling primitives that have no idea what the server is actually telling you. You configure a number, you guess, you over- or under-throttle.
- `axios-rate-limit`, `axios-retry`: tied to axios, opinionated, and largely focused on retry semantics rather than respecting server-signaled budgets.
- DIY: a `setTimeout` chain in every codebase, re-invented per project, usually wrong under burst conditions.

The gap: there is no small, dependency-free, fetch-compatible client that **reads the rate-limit headers the server is already sending** (`X-RateLimit-Remaining`, `X-RateLimit-Reset`, `Retry-After`, RFC 9331 `RateLimit` fields) and adapts in real time.

RateGuard fills that gap. It is a thin wrapper around any fetch-compatible function that observes response headers, maintains a budget per host, queues or fails or circuit-breaks on 429, and gets out of your way otherwise.

## 2. Non-Features (Explicit)

This matters more than the feature list. RateGuard is **not**:

- Not a retry library. We do not retry on 5xx, network errors, or timeouts. Compose with `p-retry` or similar.
- Not an HTTP client. It wraps one. Bring your own fetch.
- Not a rate-limit server. Client-side only. If you need server-side enforcement, look at `express-rate-limit`, Cloudflare, or a token service.
- Not a dashboard or UI. Observability is a hook surface, not a product.
- Not a polyglot package. TypeScript / JavaScript only. A Rust port may happen as a separate crate.
- Not multi-process coordinated. Single-process, single-consumer assumption. Distributed coordination is out of scope (see Open Questions).

## 3. Target User

**Persona:** A TypeScript developer building a backend, agent, or scraper that calls one or more rate-limited APIs (GitHub, OpenAI, Stripe, Shopify, Linear, internal services behind a gateway).

**What they want:**
- To call `fetch(url)` and have the right thing happen.
- Predictable behavior under burst load.
- To not read 40 pages of docs to configure it.

**What they currently do:**
- Eyeball the API's published limits, hardcode a `setTimeout` with a fudge factor.
- Catch 429s with a generic retry library and hope.
- Build a bespoke queue per integration.

**What they hate:**
- Libraries that bring 11 transitive dependencies for a 200-line problem.
- Configuration objects with 30 keys, 28 of which they will never touch.
- Hidden global state.
- Types that are technically correct but useless in practice.

## 4. API Surface

The API surface **is** the product. Treat this section as load-bearing.

### 4.1 Construction

```ts
import { RateGuard } from 'rateguard';

const client = new RateGuard(fetch, {
  defaultBudget: { tokensPerMinute: 600 },
  hosts: {
    'api.github.com':  { tokensPerHour: 5000 },
    'api.openai.com':  { tokensPerMinute: 3500 },
  },
  on429: 'queue',          // 'queue' | 'fail' | 'circuit-break'
  retryAfterMs: 'auto',    // 'auto' | number | (res: Response) => number
});

const res = await client.fetch('https://api.github.com/repos/x/y');
```

### 4.2 Types (sketch)

```ts
type FetchLike = (input: RequestInfo | URL, init?: RequestInit) => Promise<Response>;

interface RateGuardOptions {
  defaultBudget?: Budget;
  hosts?: Record<string, Budget>;
  on429?: 'queue' | 'fail' | 'circuit-break';
  retryAfterMs?: 'auto' | number | ((res: Response) => number);
  headerProfile?: 'auto' | 'github' | 'rfc9331' | 'x-ratelimit' | HeaderParser;
  onEvent?: (e: RateGuardEvent) => void;
  clock?: () => number;    // for tests
}

type Budget =
  | { tokensPerSecond: number }
  | { tokensPerMinute: number }
  | { tokensPerHour:   number }
  | { tokens: number; refillMs: number };

interface RateGuardEvent {
  type: 'queued' | 'released' | 'throttled' | '429' | 'circuit-open' | 'circuit-close';
  host: string;
  waitMs?: number;
  remaining?: number;
}
```

### 4.3 Instance methods

```ts
client.fetch(input, init?): Promise<Response>;
client.budget(host): { remaining: number; resetAt: number };
client.snapshot(): Record<string, BudgetSnapshot>;
client.drain(): Promise<void>;        // resolve when all queued requests settle
client.reset(host?): void;            // clear state, for tests
```

### 4.4 Header parsing

Auto-detects the three common dialects: GitHub-style (`X-RateLimit-*`), RFC 9331 (`RateLimit`, `RateLimit-Policy`), and plain `Retry-After`. Users may supply a custom `HeaderParser` per host.

### 4.5 Behavior contract

- A request is admitted when the local token bucket has capacity AND no circuit is open for the host.
- On response, headers update the local view of remaining budget. Server truth wins over local estimate.
- On 429: behavior follows `on429`. `queue` waits for `retryAfterMs`. `fail` rejects with `RateLimitError`. `circuit-break` opens a per-host circuit for `retryAfterMs`, fails subsequent requests fast until close.

## 5. Design Principles

1. **Zero-allocation hot path.** No object literals, no array spreads, no closures created per request in the steady state. Allocations are reserved for the slow path (queueing, 429s).
2. **Fetch-compatible.** Drop-in for `fetch`. If your code calls `fetch(url, init)`, it calls `client.fetch(url, init)`. No new mental model.
3. **No runtime dependencies.** Dev-time only. The published bundle imports nothing.
4. **Bundle budget: under 8 KB min+gzip.** Enforced in CI via `size-limit`.
5. **Cross-runtime.** Node 20+, Bun 1.0+, Deno 1.40+, modern browsers. No Node-specific APIs in the core (no `process`, no `Buffer`).
6. **TypeScript-first.** Types are a feature, not a chore. Excellent inference, strict mode clean, no `any` in the public surface.
7. **Boring failure modes.** Misconfiguration throws at construction, not on the 500th request.

## 6. Constraints

| Constraint | Value |
|---|---|
| Minimum Node | 20.0 (for stable `fetch` + AbortSignal.timeout) |
| Minimum Bun | 1.0 |
| Minimum Deno | 1.40 |
| Browsers | Last 2 versions of evergreen (Chromium, Firefox, Safari) |
| Bundle size (min+gz) | < 8 KB |
| Runtime dependencies | 0 |
| Dev dependencies | tsup, vitest, size-limit, biome (no test framework sprawl) |
| License | MIT |
| Module formats | ESM (primary), CJS (interop), .d.ts |

## 7. Non-Goals

(Beyond the non-features in section 2.)

- No transient-error retry. (Use `p-retry`.)
- No request deduplication / caching. (Use `swr`, `tanstack-query`.)
- No request signing, auth, or middleware chain. (Wrap your own.)
- No global singleton. Each `RateGuard` is its own state.
- No telemetry by default. The `onEvent` hook is opt-in and synchronous; users wire it to whatever they want.
- No multi-process / distributed coordination in v1.

## 8. Comparison to Existing Libraries

| Library | Reads server headers | Fetch-compatible | Deps | Bundle | Notes |
|---|---|---|---|---|---|
| `bottleneck` | No | No | 0 | ~30 KB | General queue, no server awareness |
| `p-queue` | No | No | few | ~5 KB | Pure client-side concurrency |
| `limiter` | No | No | 0 | small | Token bucket primitive only |
| `axios-rate-limit` | Partial | Axios only | 1 | small | Tied to axios |
| `got` (retry options) | Partial | No (got-only) | many | large | Bundled with full HTTP client |
| **RateGuard** | **Yes** | **Yes** | **0** | **< 8 KB** | The point of this library |

## 9. Versioning and Stability

- SemVer.
- Pre-1.0 (0.x): minor releases may introduce breaking changes. Patch releases are bugfix only.
- 1.0 commits to strict SemVer. Public surface = anything exported from the package entry. Internal modules are not public, even when reachable.
- Deprecations: documented for at least one minor before removal at the next major.
- Node, Bun, Deno minimum versions may be bumped in minors pre-1.0, in majors post-1.0.

## 10. Success Signal

This is OSS. Adoption shape over time:

- v0.1 lands: clean install, working examples for GitHub + OpenAI, docs site live.
- 90 days: 1,000 weekly npm downloads, 100 GitHub stars, at least one external blog post or comparison piece.
- 12 months: 10,000 weekly downloads, presence in 1+ well-known agent or scraping framework, zero unresolved correctness bugs older than 30 days.
- Qualitative: maintainers of API SDKs recommend RateGuard rather than rolling their own queue.

Anti-signals worth watching: high issue volume on "why does this not retry 5xx" suggests doc clarity failure on scope. High issue volume on multi-process coordination suggests v2 scope.

## 11. Open Questions

1. **Distributed coordination.** Worth a v2 with a pluggable `Store` interface (Redis, etc.), or stay single-process forever and let users wrap?
2. **Streaming responses.** Do we hold a budget slot for the full duration of a streamed response (correct for token-cost APIs) or release on headers (correct for request-count APIs)? Per-host config, or per-request hint?
3. **Priority queues.** Some users will want to mark a request as high-priority and jump the queue. Worth the API complexity in v1, or v1.x?
4. **Browser story.** Useful in the browser, but `fetch` in browsers has its own quirks (CORS, no access to some headers). How loudly do we advertise browser support?
5. **`Retry-After` as a Date.** Spec allows HTTP-date format. Parse it, or document as unsupported in v0.1?
6. **AbortSignal semantics during queueing.** If a request is queued and the caller aborts, do we settle as `AbortError` (yes, almost certainly) and does that count against the budget (no)?

---

*Comments welcome on the issue tracker. Targeting v0.1 tag once sections 4, 5, and 9 are stable.*
