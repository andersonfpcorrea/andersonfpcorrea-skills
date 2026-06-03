# Chapter 21: Handling Overload

## Core Idea
Overload is inevitable at scale. Systems must handle it gracefully: serve degraded responses when possible, shed load when necessary, and use client-side adaptive throttling to prevent backends from spending all resources rejecting requests.

## Frameworks Introduced
- **Adaptive Throttling** (client-side self-regulation):
  ```
  rejection_probability = max(0, (requests − K × accepts) / (requests + 1))
  ```
  - `requests`: total attempted by client in last 2 minutes
  - `accepts`: total accepted by backend in last 2 minutes
  - `K`: multiplier (typically 2); higher K = more aggressive throttling
  - When backend starts rejecting, client probability of local rejection increases. Prevents backends from wasting resources on rejection processing.

- **Criticality levels** (four tiers for RPC prioritization):
  1. **CRITICAL_PLUS**: Never fail; most severe impact if dropped.
  2. **CRITICAL**: Default for user-facing RPCs; should not fail in most circumstances.
  3. **SHEDDABLE_PLUS**: Some quality degradation acceptable.
  4. **SHEDDABLE**: Background/batch work; can be dropped freely under load.

## Reference Tables

| Strategy | When to Use | Mechanism |
|---|---|---|
| Serve degraded results | Moderate overload | Simpler algorithm, subset of data |
| Load shedding | Heavy overload | Reject requests when CPU/memory/queue exceeds threshold |
| Client-side throttling | Backend overwhelmed by rejections | Adaptive throttling formula above |
| LIFO queuing | High arrival rate, many stale requests | Replace FIFO with LIFO or CoDel to drop stale requests |
| Deadline propagation | Multi-tier RPC trees | Propagate remaining deadline through each RPC hop |

## Key Concepts
- **QPS as a flawed capacity metric**: Request cost varies widely by type, time, and client. CPU consumption is the correct signal.
- **Retry amplification danger**: Naive retries compound overload. If frontend retries 3×, backend retries 3×, and database retries 3×, one user action generates 27 database requests. Always use randomized exponential backoff + retry budget.
- **Deadline propagation**: If frontend sets a 30s deadline and takes 7s processing, the backend RPC should have a 23s deadline, not a fresh 30s. Without propagation: backends do work that clients have already abandoned.
- **Deadline cancellation**: When client gives up, propagate cancellation downstream to prevent wasted backend work.
- **LIFO vs FIFO under load**: FIFO queues serve stale requests (client may have retried). LIFO or CoDel serves freshest requests, maximizing useful work per CPU.
- **Utilization signals**: Use CPU utilization as the overload signal, not queue length alone. CPU is more direct and stable.
- **Backend quota management**: Each customer allocated CPU-seconds/second. Backend enforces per-customer limits in real time using global aggregate state.

## Mental Models
- "Redirect when possible, serve degraded results when necessary, handle resource errors transparently when all else fails."
- Client-side throttling prevents backends from becoming a "request-rejecting machine" — rejections still consume resources.
- Every retry has a cost. At 3 retry layers each with 3 attempts, one user action = 27 backend calls. Budget retries explicitly.
- A request past its deadline should be abandoned by the server, not completed — no credit for late work in distributed systems.

## Anti-patterns
- **Unlimited retries without backoff**: Causes retry storms that amplify overload into full cascade.
- **FIFO queues under sustained load**: Serves stale requests that clients have already given up on.
- **Hardcoded deadlines in intermediate services**: Should use deadline propagation from the root request.
- **Treating all requests equally**: Criticality levels exist precisely so CRITICAL traffic succeeds while SHEDDABLE is dropped.

## Key Takeaways
1. Use adaptive throttling (client-side) to prevent backends from wasting resources on rejections when overloaded.
2. Always use randomized exponential backoff for retries; cap retries per request AND per process/service.
3. Propagate deadlines through every RPC hop — servers must check remaining deadline before doing work.
4. Assign criticality to all RPCs — under overload, SHEDDABLE is dropped first, CRITICAL is preserved.
5. CPU consumption is the correct capacity metric, not QPS — request cost is too variable.

## Connects To
- **Ch 20**: Load balancing — load balancing minimizes overload; this chapter handles when it occurs anyway.
- **Ch 22**: Cascading failures — overload + bad retry behavior is the primary cascade trigger.
- **Ch 19**: Frontend load balancing — DNS/VIP routing is the first line of overload distribution.
