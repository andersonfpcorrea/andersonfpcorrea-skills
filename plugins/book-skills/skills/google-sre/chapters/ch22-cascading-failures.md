# Chapter 22: Addressing Cascading Failures

## Core Idea
Cascading failures grow via positive feedback — overloaded servers fail, their load moves to healthy servers, which then fail. Design defenses at every layer; when a cascade starts, immediate steps to escape it are counter-intuitive (drop more traffic, not less).

## Frameworks Introduced
- **Cascade Trigger → Amplifier pattern**:
  - Cluster B fails → Cluster A receives extra load → A servers crash → more load to remaining A servers → total collapse.
  - Key insight: if 10% of servers survive, you need to drop to 10% of normal traffic to stabilize, not back to 90%.

- **Defense-in-depth strategy** (cascading failure prevention, in priority order):
  1. Load test to failure — know your actual breaking points
  2. Serve degraded results (graceful degradation)
  3. Reject requests early when overloaded (load shedding)
  4. Rate-limit at higher-level systems (reverse proxies, load balancers)
  5. Capacity planning at N+2 minimum

- **GC Death Spiral**: Java-specific cascade: high load → more GC → less CPU → slower requests → more RAM needed → less cache → more backend calls → more GC. Break by reducing load.

## Reference Tables

| Resource | Exhaustion Effect | Cascade Mechanism |
|---|---|---|
| CPU | All requests slow | More in-flight requests → more memory → cache miss → more backend calls |
| Memory | Tasks evicted | Crash → load redistribution |
| Threads | Health checks fail | Load balancer marks server unhealthy → redistributes load |
| File descriptors | Can't accept connections | Server appears down → load redistributed |

**Immediate steps to escape a cascade (in order)**:
1. Increase resources (if feasible quickly)
2. Stop health-check-induced traffic redistribution to avoid accelerating the cascade
3. Restart servers (if crash-looping is the problem)
4. Drop traffic — often the only way to stop positive feedback
5. Enter emergency mode (degraded responses)
6. Eliminate bad traffic (retries, batch jobs, non-critical work)

## Key Concepts
- **Retry amplification**: 3 layers × 3 retries each = 27× multiplication of load at the bottom layer. With exponential backoff + jitter + budget, contained. Without: cascade accelerator.
- **Deadline propagation and cancellation**: Work done after a client deadline has passed is wasted. Propagate deadlines; cancel RPCs when client gives up.
- **Queue management**: Small queues (≤50% of thread pool) cause fast rejection under overload. Large queues cause latency explosion before rejection. LIFO/CoDel drops stale requests.
- **Cold cache on startup**: A freshly started server has empty caches → more backend calls → potential cascade when restarting after failure. Use gradual traffic ramp-up.
- **Slow startup problem**: Freshly started servers need time to reach steady-state performance. Don't bombard them immediately.
- **Testing for cascades**: Deliberately overload in staging to find the failure mode before production does.

## Code Examples
```go
// BAD: Naive retry without backoff
for attempts > 0 {
    response, err := client.MakeRequest(ctx, request)
    if err != nil { attempts--; continue }  // Amplifies overload
    return response
}

// GOOD: Exponential backoff with jitter and budget
for attempts > 0 && budget.HasRetries() {
    response, err := client.MakeRequest(ctx, request)
    if err != nil {
        if !isRetriable(err) { return nil, err }
        sleepWithJitter(exponentialBackoff(attempt))
        attempts--
        continue
    }
    return response
}
```

## Anti-patterns
- **Assuming load drop = recovery**: At 10% server capacity, dropping to 90% of normal load won't stabilize. Drop to 10%.
- **Retrying non-retriable errors**: Permanent failures (malformed requests) should never be retried.
- **Multi-layer retries without coordination**: Each layer retrying independently creates multiplicative amplification.
- **Greedy queue algorithms under load**: FIFO serves oldest (likely stale) requests while newest (fresh) requests time out.

## Key Takeaways
1. When a cascade starts, counter-intuitively you must drop MORE traffic (to the stable point) not less.
2. Retries without exponential backoff + jitter + budget are a cascade accelerator. Budget retries globally.
3. Propagate deadlines through all RPC hops — servers should abandon work past the deadline.
4. The GC death spiral is Java-specific but illustrates how resource exhaustion types chain together.
5. Load test to failure in staging — you must know your breaking points before production does.

## Connects To
- **Ch 21**: Handling overload — the per-service mechanisms that prevent individual server overload.
- **Ch 20**: Load balancing — lame duck state prevents health-check churn from accelerating cascades.
- **Ch 13**: Emergency response — cascade failures are the most severe emergency type.
