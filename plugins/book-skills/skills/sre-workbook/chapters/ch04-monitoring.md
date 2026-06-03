# Chapter 4: Monitoring

## Core Idea
Good monitoring is purposeful — every metric should answer a specific question about the system's behavior. Configuration-as-code and loose coupling make monitoring systems maintainable at scale.

## Frameworks Introduced
- **Purposeful metrics**: Every metric you track should serve one of four purposes: intended changes, dependencies, saturation, or status of served traffic. Metrics without a clear purpose create alert noise and cognitive load.
- **Monitoring configuration as code**: Version-control your monitoring config; apply code review; treat it as a production artifact. This enables consistency, auditability, and rollback.

## Key Concepts
- **Speed**: How quickly does monitoring detect and surface an issue? Faster is better; optimize for low detection time on SLO-threatening events.
- **Calculations**: Aggregations, rate calculations, percentiles — the monitoring system must perform these efficiently; avoid shifting all compute to dashboards at query time.
- **Loose coupling**: Monitoring systems should not depend tightly on the services they monitor. Use standard exporters and protocols (e.g., Prometheus scrape endpoints) so the monitoring pipeline survives service restarts.
- **Alert consistency**: Alerts should fire on the same conditions across all environments (dev, staging, prod). Inconsistent alerting erodes trust.

## Sources of Monitoring Data

| Source | Use case | Trade-offs |
|--------|----------|-----------|
| Application server logs | Request counts, error classification | Rich but high volume; may miss pre-backend failures |
| Load balancer metrics | Near-user signal for availability/latency | Closer to user experience; coarser error classification |
| Black-box probers | Detects complete failures from outside | Misses partial failures; synthetic ≠ real traffic |
| Client-side instrumentation | True user-experience signal | Complex to deploy; needs its own reliability |
| Infrastructure metrics | CPU, memory, disk, network | Useful for saturation; not user-facing |

## Metrics with Purpose

- **Intended changes**: Did a deployment change key metrics? Track the metrics that releases are expected to affect so you can confirm or deny intent.
- **Dependencies**: Track error rates and latencies of external dependencies; this isolates root causes during incidents.
- **Saturation**: Track utilization against capacity limits (CPU, memory, connection pool, queue depth) to get advance warning before requests fail.
- **Status of served traffic**: SLI metrics — request rates, error rates, latency histograms. These are the metrics that feed into SLO alerting (Ch 5).

## Testing Alerting Logic
- Test your alerting config before deploying to production; push synthetic data to validate alert conditions fire when expected and don't fire when they shouldn't.
- Treat flapping alerts (alerts that resolve on their own without action) as bugs in the alerting config, not in the system.

## Anti-patterns
- **Metrics without purpose**: collecting everything "just in case" creates noise; every metric incurs storage and query cost.
- **Tight coupling**: monitoring systems that embed service-specific logic break when the service changes.
- **Inconsistent monitoring**: different setups in dev/staging/prod mean you can't trust monitoring to catch production-only bugs.

## Key Takeaways
1. Every metric should serve one of: intended changes, dependency health, saturation, or traffic status.
2. Treat monitoring config as code: review, version, and test it.
3. Prefer loose coupling — standard exporters over custom integrations.
4. Test alerting logic on synthetic data before going live.

## Connects To
- **Ch 5**: Alerting on SLOs uses the "status of served traffic" metrics defined here
- **Ch 9**: Incident response relies on monitoring to detect and characterize outages
