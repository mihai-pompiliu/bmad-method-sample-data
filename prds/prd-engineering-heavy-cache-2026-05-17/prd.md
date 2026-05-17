# PRD: Quorum Distributed Cache (Redis Cluster Replacement)

## Document Control

- Title: Quorum Distributed Cache, Phase 1 Production Cutover
- Owning Team: Platform Caching
- Author: Principal Engineer, Platform Caching
- Reviewers: Architecture Review Board (ARB), Storage SIG, SRE Foundations, Security Platform
- Status: Draft, pre-ARB
- Revision: 0.4
- Related Docs: ADR-0142 (Raft selection), ADR-0151 (RESP3 compatibility shim), CAP-2024-Q3 (capacity model), RFC-Quorum-Protocol-v1
- Distribution: internal, infra-eng

## Background

The current cache tier is Redis Cluster, deployed across 28 shards in two regions (us-east-1 and us-west-2). Versions are mixed: 17 shards on Redis 6.2.x, 11 shards on Redis 5.0.x carried forward from the 2021 build-out. Working set across the fleet is approximately 340GB hot, with peak aggregate throughput of 3.1M ops/sec measured at the 95th percentile of the daily curve. Replication is single-region primary/replica with async cross-region copy via a custom side-car (`xreplica`) that has accumulated several known consistency edge cases under partition.

Operational pain points motivating replacement:

1. No multi-region active-active. Failover is operator-driven and has run 9 to 14 minutes in the last three GameDay exercises.
2. Mixed-version fleet blocks Cluster API features (notably RESP3 push, client-side caching) and creates a long tail of CVE patching cost.
3. `xreplica` is unowned. The original authors have left. It has produced three SEV-3 incidents in the last 18 months.
4. Memory efficiency. jemalloc fragmentation on long-running 5.0 shards has measured between 1.31 and 1.58 RSS/used ratio, costing roughly 22 percent of provisioned memory.
5. No native tenancy primitives. Namespace isolation is enforced in client libraries, inconsistently.

Quorum is the in-house replacement, developed by Platform Caching over the last two quarters. This PRD covers Phase 1: production cutover for the 11 legacy Redis 5.0 shards, representing approximately 38 percent of working set.

## Goals

- Replace the 11 Redis 5.0 shards with Quorum clusters in both regions.
- Deliver multi-region active-active replication via Raft per partition.
- Maintain or improve current in-region p99 latency.
- Provide a wire-compatible path (RESP3) so existing clients require no immediate change.
- Land a gRPC + protobuf surface for new clients and for features RESP3 cannot express.
- Stand up an OTel-native observability surface from day one.

## Non-Goals (Technical)

- Replacing the Redis 6.2 shards in this phase. Phase 2 will address them after Quorum has 90 days of production soak.
- Supporting Redis Streams (`XADD`, `XREAD`, consumer groups). No internal customer is on Streams in the 5.0 fleet; confirmed by namespace audit.
- Supporting geospatial commands (`GEOADD`, `GEOSEARCH`). Same audit result.
- Lua scripting (`EVAL`, `EVALSHA`). Replaced by a server-side function registry in a later phase.
- Disk-backed persistence. Quorum is memory-resident. Cold start rebuilds from peers; durable storage is explicitly out of scope.
- Client library rewrites. Existing `go-redis`, `jedis`, and `redis-py` clients must continue to work via the RESP3 shim.

## System Architecture Overview

Quorum is a memory-resident key-value store organized as a set of Raft groups, one per partition. The partition map is a consistent hash ring with 4096 virtual nodes, assigned to physical Quorum nodes by the control plane. Each partition runs a 5-member Raft group spanning both regions (3 in the primary region for the partition, 2 in the secondary), with leader placement biased to minimize cross-region writes per partition.

```
                      +-------------------------+
                      |   Control Plane (etcd)  |
                      |  partition map, leases  |
                      +-----------+-------------+
                                  |
                +-----------------+-----------------+
                |                                   |
        Region: us-east-1                   Region: us-west-2
   +-----------------------+             +-----------------------+
   |  Quorum Node Pool     |  Raft RPC   |  Quorum Node Pool     |
   |   - partition 0..N    | <---------> |   - partition 0..N    |
   |   - RESP3 listener    |  (mTLS)     |   - RESP3 listener    |
   |   - gRPC listener     |             |   - gRPC listener     |
   +----------+------------+             +----------+------------+
              |                                     |
        Client Proxy                          Client Proxy
        (RESP3 + dual-write)                  (RESP3 + dual-write)
              |                                     |
        Application Pods                      Application Pods
```

Components:

- Control Plane. etcd-backed metadata service. Holds partition assignments, node membership, lease state. Not in the data path.
- Quorum Node. Stateful pod. Hosts N partition replicas. Single-process, multi-threaded. io_uring on Linux 6.1+, with BPF-based socket steering to pin connections to worker shards.
- Client Proxy (`qproxy`). Sidecar deployed per application pod during migration. Speaks RESP3 to the application, routes to Redis Cluster, Quorum, or both (dual-write). Removed after cutover.

### Raft Group Topology

- 5 members per partition.
- 3 in the partition's home region, 2 in the peer region.
- Leadership is sticky to the home region under normal operation. Leader can fail over to the peer region under home-region loss.
- Pre-vote enabled. Leader leases of 500ms. Heartbeat interval 50ms. Election timeout randomized 1000 to 1500ms.
- Snapshot every 100k log entries or 256MB, whichever first. Snapshots streamed via dedicated TCP connection, not the Raft RPC channel.

### Consistency Model

- Single-key operations: linearizable within a partition (Raft log order is the linearization point).
- Multi-key operations within a single tenant namespace: sequential consistency. Cross-partition transactions are not supported in Phase 1.
- Cross-region: by default, all writes go through Raft and are linearizable across regions for that partition. Per-key opt-in to "eventual" mode is available via a key tag (`{eventual}`) for cache-style use cases where staleness is tolerable. Eventual keys replicate via async log shipping, not Raft.
- Reads: by default, leader reads. Follower reads are opt-in per call with a bounded-staleness parameter (max 200ms).

## API Surface

Two listeners. Same data, two protocols.

### RESP3 (Compatibility)

- Wire-compatible with Redis 7 RESP3.
- Supported commands (Phase 1):
  - Strings: `GET`, `SET`, `SETEX`, `SETNX`, `MSET`, `MGET`, `INCR`, `INCRBY`, `DECR`, `DECRBY`, `APPEND`, `GETRANGE`, `STRLEN`.
  - Hashes: `HSET`, `HGET`, `HMGET`, `HGETALL`, `HDEL`, `HEXISTS`, `HINCRBY`, `HLEN`, `HKEYS`, `HVALS`.
  - Sorted sets: `ZADD`, `ZRANGE`, `ZRANGEBYSCORE`, `ZRANGEBYLEX`, `ZREM`, `ZSCORE`, `ZCARD`, `ZINCRBY`, `ZRANK`.
  - HyperLogLog: `PFADD`, `PFCOUNT`, `PFMERGE`.
  - TTL: `EXPIRE`, `EXPIREAT`, `PEXPIRE`, `TTL`, `PTTL`, `PERSIST`.
  - Server: `PING`, `CLIENT`, `HELLO`, `RESET`, `INFO` (subset).
- Explicitly unsupported: `XADD`/`XREAD`/`XGROUP`, `GEOADD`/`GEOSEARCH`, `EVAL`/`EVALSHA`, `SUBSCRIBE`/`PUBLISH` (deferred to Phase 3), `MULTI`/`EXEC` (deferred), `CLUSTER` admin (proxied to no-op or rewritten by `qproxy`).
- Behavior delta: `OBJECT ENCODING` returns Quorum-native encoding names. Clients that key off encoding strings will need to update. The audit found 2 such call sites; both owned by Platform Search.

### gRPC + Protobuf (Native)

- New surface for clients that want richer types and observability.
- Service definitions in `qcache.v1.proto`.
- Methods cover the same data operations plus:
  - `BatchGet`/`BatchSet` with per-key consistency hints.
  - `Watch` (server-stream) for key invalidation push.
  - `Lease` and `LeaseRenew` for distributed-lock-style usage.
  - `Tenant.*` admin RPCs (rate limit config, quota inspection).
- All gRPC RPCs carry an `x-tenant-id` header validated against the partition map and the tenant ACL.

## Data Model

- Flat keyspace per tenant. Keys are bytes, max 4KB.
- Values are bytes, max 16MB per value. Sorted sets and hashes have a per-collection cap of 64MB.
- Memory accounting:
  - 16-byte fixed overhead per key (pointer, type tag, expiry, ref count).
  - Slab allocator with 14 size classes: 32, 64, 96, 128, 192, 256, 384, 512, 1024, 2048, 4096, 8192, 16384, 65536 bytes. Allocations above 64KB fall through to the system allocator.
  - Slab class utilization is a first-class metric (`quorum_slab_util_ratio{class="..."}`).
- TTL: per-key expiry, second and millisecond precision. Expiration runs on a background reaper at 100Hz; lazy expiry on access guarantees no stale read returns a logically expired key.
- Eviction: per-tenant LRU with a configurable working set cap. No global eviction; tenants that exceed their cap are rejected with `QUOTA_EXCEEDED`, not silently evicted from other tenants.

## Performance Targets and SLOs

In-region (single datacenter, warm cache, 90 percent reads):

- p50 < 250µs end-to-end (client wire to server wire).
- p99 < 2ms.
- p99.9 < 10ms.
- Sustained throughput per shard cluster: 1.2M ops/sec. Burst (60s window): 2.4M ops/sec.

Cross-region (linearizable write through Raft):

- p50 < 25ms (driven by inter-region RTT, currently 18ms 95th).
- p99 < 80ms.
- p99.9 < 200ms.

Eventual-mode keys cross-region:

- p99 replication lag < 250ms.
- p99.9 replication lag < 1.5s under healthy network.

Availability SLO:

- 99.99 percent monthly for in-region single-key reads.
- 99.95 percent monthly for cross-region linearizable writes.

Error budget consumption is tracked per partition and aggregated per tenant.

## Reliability and Failure Modes

| Failure | Detection | Response | Time to Restore |
|---|---|---|---|
| Single node failure | Raft heartbeat loss (3 missed, 150ms) | Follower promoted on partitions where node was leader | < 2s for leader re-election; reads served from peers immediately |
| Disk full on a node (snapshot dir) | Disk metric < 5GB free | Node enters read-only; control plane reassigns partitions | < 5 min via partition drain |
| Network partition, minority side | Raft loses quorum | Minority partitions reject writes, serve stale reads if opted in | Restored when partition heals |
| Network partition, majority side | Raft retains quorum | Continues serving | n/a |
| Full region failure | Control plane health check + Raft membership view | Partitions whose home is the lost region elect leader in peer region | < 30s for leader election fleet-wide |
| Control plane (etcd) unavailable | etcd client errors | Data plane continues with cached partition map; no new placement decisions | Up to 10 min cached, then degraded mode |
| qproxy crash | Pod readiness | Sidecar restarts; application connections re-establish | < 5s |
| Slow client (head-of-line) | Per-connection write buffer > 16MB | Connection closed with `OUTPUT_BUFFER_LIMIT` | immediate |
| Hot key | Per-key ops counter > 100k/s | Read replica fanout enabled for that key, surfaced to tenant dashboard | < 30s |

## Deployment Model

- Kubernetes 1.29+ required (uses `PodDisruptionBudget` v1 with `unhealthyPodEvictionPolicy: AlwaysAllow`).
- StatefulSet per region per cluster, with stable network identity via headless service.
- Pod spec:
  - 32 vCPU, 256GB RAM, local NVMe (snapshot scratch only, not durable store).
  - Hugepages 2MB, 200 pages reserved.
  - `CAP_BPF`, `CAP_NET_ADMIN` for BPF socket steering programs.
- Linux kernel 6.1 minimum (io_uring features, BPF CO-RE).
- Node image: internal "platform-base-2026.04" with io_uring, BPF toolchain, and tuned scheduler profile.
- Rollouts: one partition replica at a time, with a 60s soak between replicas. Full cluster rollout takes approximately 45 minutes.

## Observability Requirements

- OpenTelemetry SDK embedded. Spans for every RPC, with sampling at 1 percent by default, 100 percent on error.
- Prometheus exposition on `/metrics`, scrape interval 15s.
  - RED metrics per RPC method.
  - Raft metrics: leader changes, log lag, snapshot count, commit latency.
  - Slab metrics: utilization, fragmentation ratio, eviction rate.
  - Per-tenant counters: ops, bytes, quota usage.
- Jaeger trace export via OTLP. Cross-region trace propagation via W3C `traceparent`.
- Structured logs in JSON, shipped via Fluent Bit to the central log bus.
- Required dashboards (delivered as code in `quorum-dashboards/`):
  - Fleet overview
  - Per-cluster RED + Raft health
  - Per-tenant usage and SLO burn
  - Migration progress (Phase 1 specific)

## Security Considerations

- mTLS on all listeners. Internal CA. Cert rotation every 24 hours via SPIFFE workload identity.
- Tenant authentication via signed tenant tokens issued by the internal auth service. Tokens scoped to a tenant ID and a TTL of 1 hour.
- Authorization at the partition router: every RPC is checked against tenant ACL before being dispatched.
- At-rest: not applicable, memory-resident. RAM scrubbing on partition release (`MADV_FREE` followed by explicit zeroing for tenants with `secure: true`).
- In-flight: TLS 1.3 only. RESP3 listener requires AUTH followed by HELLO; no anonymous access.
- Audit log: every admin RPC (`Tenant.*`, partition rebalance, snapshot trigger) emitted to the central audit pipeline.

## Capacity Planning

Phase 1 targets the 11 Redis 5.0 shards, with combined hot working set of approximately 129GB and peak 1.18M ops/sec aggregate.

Sizing:

- 6 Quorum nodes per region, 256GB RAM each, 70 percent target utilization. Total 12 nodes across two regions.
- Headroom for 40 percent organic growth over 12 months and 2x burst.
- Partition count: 1024 (subset of the 4096 ring), bound to the Phase 1 tenants.

Cost model (vs. current Redis 5.0 fleet):

- Compute: +18 percent (denser nodes but new active-active footprint).
- Memory: -22 percent (fragmentation recovery + slab efficiency).
- Operational ($/incident estimated): -60 percent (target, modeled on `xreplica` incident history).

Net: roughly flat infrastructure spend in Phase 1; the win is operational.

## Migration Plan

Phased per namespace. 14 namespaces in the 5.0 fleet, ordered by risk (lowest first).

1. Pre-flight (week 0)
   - Deploy `qproxy` sidecar to a canary application pod per namespace.
   - Configure `qproxy` in shadow mode: every read served from Redis, also issued to Quorum, responses compared, divergences logged.
   - Run for 72 hours minimum, divergence rate must be < 0.01 percent.

2. Dual-write (weeks 1 to 4, staggered)
   - Switch `qproxy` to dual-write. Reads still from Redis.
   - Per-key TTLs honored on both sides.
   - Quorum considered hot when 99.9 percent of expected keys present, verified by working-set sampling.

3. Cutover (weeks 4 to 7, staggered)
   - Per namespace: flip reads to Quorum, writes remain dual.
   - 24-hour soak.
   - Disable Redis writes. Redis becomes read-only fallback for 7 days.
   - Decommission Redis shards for that namespace.

4. Post-cutover (week 8+)
   - `qproxy` removed from applications, native clients (RESP3 or gRPC) talk directly to Quorum.
   - Redis 5.0 shards torn down.

Cutover gates (all must pass per namespace before advancing):

- Divergence rate < 0.01 percent over the prior 24 hours.
- p99 latency on Quorum within 10 percent of the Redis baseline.
- No SEV-3+ incidents attributable to Quorum in prior 7 days.
- Tenant owner sign-off.

## Rollback Plan

Reversible at every phase up through "Disable Redis writes."

- Shadow mode: no-op rollback (Redis is still authoritative).
- Dual-write: flip `qproxy` reads back to Redis. Quorum data discarded for that namespace.
- Reads on Quorum: flip `qproxy` reads back to Redis, retain dual-write. Investigate divergence.
- Writes Redis-only: re-enable Redis writes within the 7-day fallback window. After 7 days, rollback requires a forward-fix (replay from Quorum to Redis via a one-shot tool, not yet built and not in scope for Phase 1).

Rollback authority: on-call SRE for the affected namespace, no further approvals.

## Dependencies

- Kubernetes 1.29+ in both regions. (us-east-1 at 1.30, us-west-2 at 1.29.2. OK.)
- Linux kernel 6.1+ on all data-plane nodes. (Currently 6.1.62 on the platform-base image.)
- BPF toolchain in the node image (CO-RE, libbpf 1.4+).
- etcd 3.5+ for the control plane. (Existing platform etcd cluster, capacity confirmed.)
- Internal auth service v2 (SPIFFE workload identity). Available, GA Q1.
- OTel collector fleet. Available.
- Inter-region network: 18ms p95 RTT, 9.7 Gbps available. Confirmed with Network Eng.

## Open Issues (Technical)

1. `OBJECT ENCODING` semantics. Two callers (Platform Search) inspect encoding strings. Either change them or expose a compatibility map. Decision needed before shadow mode.
2. `qproxy` connection pool sizing under dual-write doubles upstream connection count. Validate that Redis 5.0 shards tolerate the increase (modeling says yes, but we have not load-tested above 1.6x).
3. Snapshot streaming bandwidth. A worst-case snapshot stream is ~12GB; with the per-node 10Gbps NIC and other Raft traffic, we may need a dedicated QoS class for snapshot streams.
4. Hot key replica fanout: detection threshold currently 100k ops/sec/key. Validate against production workload traces. Possible the threshold should be tenant-relative.
5. RESP3 `CLIENT TRACKING` (server-assisted client caching) is supported in the spec but not in the Phase 1 implementation. Confirm no callers depend on it (audit indicates none, but worth a final pass).
6. Eventual-mode key tag `{eventual}` collides with the existing Redis hash-tag convention used by 3 internal libraries to control slotting. Decide whether to use a distinct sigil (`<eventual>`, `[[eventual]]`) or change semantics.
7. Phase 2 (Redis 6.2 fleet) will require streams support or a migration story for the 4 namespaces using `XADD`. Out of scope here but flagged for sequencing.
8. Cost modeling assumes 70 percent memory utilization target. SRE Foundations has asked us to revisit at 60 percent given the active-active overhead. Open.

## Appendix A: Wire Protocol Notes

- RESP3 listener negotiated via `HELLO 3`. Clients that send `HELLO 2` get a downgraded surface (no push frames, no map type). Push-frame use in Phase 1 is limited to invalidation messages for clients that opt in.
- gRPC listener supports both unary and server-streaming. Bidirectional streaming is reserved for the future pub/sub surface.
- Protobuf schema versioning: all messages tagged with `schema_version`. Server accepts N and N-1.

## Appendix B: Sizing Math (abbreviated)

Working set: 129GB hot.
Per-replica overhead: 1.15x (Raft log + snapshot scratch in RAM cache).
Per-region replication factor: 3 in home, 2 in peer = 5 total.
Effective RAM per region for hot data: 129 * 1.15 * 3 / 2 ~= 222GB.
Node capacity (256GB * 0.70 target util): ~179GB usable per node.
Nodes per region: ceil(222 / 179) = 2 minimum. Add headroom and partition spread: 6 per region.

## Appendix C: References

- ADR-0142 Raft library selection (etcd/raft fork vs. hashicorp/raft vs. in-house). Decision: etcd/raft fork.
- ADR-0151 RESP3 compatibility shim approach.
- CAP-2024-Q3 Capacity model and assumptions.
- RFC-Quorum-Protocol-v1 Wire protocol specification.
- Incident reports IR-2024-0231, IR-2024-0712, IR-2025-0118 (`xreplica` incidents motivating replacement).
