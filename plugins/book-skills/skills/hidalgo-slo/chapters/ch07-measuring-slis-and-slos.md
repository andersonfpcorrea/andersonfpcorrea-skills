# Chapter 7: Measuring SLIs and SLOs

## Core Idea
Good SLI measurement infrastructure has six properties: flexible targets, testable targets, freshness, cost efficiency, reliability of the measurement system itself, and alignment with organizational constraints. The choice between metrics and logs shapes your architecture.

## Design Goals for SLI Measurement Systems

1. **Flexible targets**: Changing the SLO percentage should not require re-instrumenting the service.
2. **Testable targets**: You must be able to validate that your measurement system correctly captures good/bad events.
3. **Freshness**: The lag between event occurrence and SLI update should be short enough for your alerting needs.
4. **Cost**: Measurement has financial and computational overhead; balance precision with cost.
5. **Reliability**: The measurement system must be more reliable than the service it measures; a broken metrics pipeline that silences alerts is dangerous.
6. **Organizational constraints**: Must integrate with existing tooling, approval processes, and access controls.

## Common Measurement Machinery

### Centralized Time-Series Statistics (Metrics)
- Ideal for high-QPS services.
- Pre-aggregated counters (good_requests, total_requests) are efficient.
- Suitable for alerting because they're fast to query.
- Limited cardinality — can't drill into individual failing requests.

### Structured Event Databases (Logging)
- Each request produces a log record with rich context (user, endpoint, latency, result).
- Enables high-cardinality analysis: find which users, regions, or endpoints are failing.
- Slower and more expensive to query than metrics for aggregate SLO math.
- Best combined with sampling for high-volume services.

## Common Implementation Cases

**Latency-sensitive request processing:**
- Instrument at the load balancer or service entry point for client-perceived latency.
- Use histogram buckets aligned to your SLO thresholds to avoid precision loss.
- Consider p50, p95, p99 rather than averages — averages hide the tail.

**Low-lag, high-throughput batch processing:**
- Measure event completeness and freshness, not just error rate.
- Track: time from event creation to successful processing; percentage of events processed within SLO window.

**Mobile and web clients:**
- Client-side instrumentation captures real user experience; server-side misses network latency.
- Real User Monitoring (RUM) and synthetic probes both have roles.
- Black-box monitors from external data centers give realistic user-perspective data.

## Integration Considerations

- **Distributed tracing**: Enables measuring latency through multi-service calls. Ideal for user-journey SLIs spanning many microservices.
- **SLI discoverability**: Measurement systems should expose SLI definitions alongside the data so other teams can understand what is being measured.

## Anti-patterns

- **Measuring at the wrong layer**: Measuring error rate at the database when users experience errors at the load balancer gives misleading data.
- **Using averages instead of percentiles for latency**: Averages hide the tail. Use percentile buckets aligned to SLO thresholds.
- **Unreliable measurement pipeline**: If the metrics system is less reliable than the service, you'll have silent failures.
- **Cost blindness**: High-cardinality logging for high-QPS services can cost as much as the service itself.

## Key Takeaways

1. Metrics (time-series) and logs (structured events) serve different purposes — use both.
2. Instrument at the outer edge closest to the user for most representative data.
3. Histograms aligned to SLO thresholds are more efficient than raw latency values.
4. The measurement system must be more reliable than the thing it measures.
5. Distributed tracing is the best approach for multi-service user-journey SLIs.

## Connects To

- **Ch 8**: How the measurement data feeds into SLO alerting
- **Ch 9**: Statistical handling of low-QPS or noisy measurements
- **Ch 3**: The conceptual SLI design that precedes this technical implementation
