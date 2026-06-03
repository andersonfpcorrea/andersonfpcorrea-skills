# Chapter 20: Load Balancing in the Datacenter

## Core Idea
In-datacenter load balancing maximizes resource utilization by distributing work evenly across backend tasks, detecting unhealthy tasks (lame duck state), and avoiding hot spots that waste reserved capacity.

## Frameworks Introduced
- **Lame Duck State**: A backend task in lame duck state is alive but signals clients to stop sending new requests. Enables graceful drain for clean shutdowns, code pushes, and maintenance.
  - States: Healthy → Lame Duck → Refusing connections
  - Implementation: SIGTERM → enter lame duck → broadcast via UDP health checks to all clients (including inactive ones)

- **Subsetting**: Each client maintains connections to only a subset of backends (not all). Reduces connection count from O(clients × backends) to manageable levels.
  - Must be done carefully — deterministic but pseudorandom subsets prevent hot spots while enabling even coverage.

- **Weighted Round Robin (WRR)**: Load balancing policy that assigns requests proportional to backend capacity. More powerful than simple round robin; allows heterogeneous hardware.

## Key Concepts
- **The ideal case**: All backend tasks consume identical CPU at any given moment. Poor distribution wastes reserved capacity — you may have 1,000 CPUs reserved but only be able to use 700 due to hot spots.
- **CPU as primary capacity metric**: Prefer CPU consumption over QPS. QPS ignores request cost variability; CPU is a direct measurement of actual work. Memory pressure shows up in CPU (GC overhead).
- **Flow control**: Track active requests per connection; if > N (typically 100), mark backend as unhealthy. Crude but effective baseline protection. Fails for slow backends.
- **Per-customer limits**: Quota per customer (CPU seconds/second). When global overload occurs, only misbehaving customers are rejected.
- **Least loaded (least requests)**: Simple policy; always send to backend with fewest active requests. Works well for homogeneous systems with similar request costs.
- **Subsetting determinism**: Subset assignments must be stable (to keep caches warm, maintain session state) but varied across clients (to avoid all clients hitting the same backends).

## Mental Models
- "Poor in-datacenter load balancing artificially limits resource availability" — waste of reserved capacity is invisible without measurement.
- Lame duck state decouples task lifecycle from request lifecycle — ongoing requests complete normally while new requests go elsewhere.
- Subsetting is the connection pool problem solved at the cluster level.

## Anti-patterns
- **QPS-based capacity modeling**: Request cost variability makes QPS a moving target. Measure CPU directly.
- **Full mesh connections**: O(clients × backends) connections at Google scale is impractical. Subsetting limits this.
- **Abrupt shutdowns**: Killing a backend task without lame duck state causes errors on all in-flight requests.
- **Uniform load balancing without health checking**: Sending requests to unhealthy or overloaded backends cascades.

## Key Takeaways
1. Lame duck state enables graceful drain — no new requests, in-flight requests complete normally. Implement in every service.
2. Measure capacity in CPU seconds, not QPS — request cost varies and QPS becomes a misleading metric at scale.
3. Subsetting limits the connection graph to manageable size while maintaining even load distribution.
4. Per-customer limits protect well-behaved clients during global overload — the misbehaving client bears the error rate.
5. Imbalanced load means wasted reserved capacity — strive for even distribution across all tasks.

## Connects To
- **Ch 19**: Frontend load balancing — the two chapters together cover the full two-level balancing system.
- **Ch 21**: Handling overload — what happens when load balancing isn't enough.
- **Ch 22**: Cascading failures — imbalanced load is a primary cascade trigger.
