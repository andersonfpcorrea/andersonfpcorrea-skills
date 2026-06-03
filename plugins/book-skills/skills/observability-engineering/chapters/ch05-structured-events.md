# Chapter 5: Structured Events Are the Building Blocks of Observability

## Core Idea
The fundamental data type for observability is the **arbitrarily wide structured event** — a single record capturing everything that occurred during one unit of work (e.g., one service request), with no schema limitations on fields.

## Frameworks Introduced
- **Event construction pattern**: Initialize an empty map at request entry → append any interesting detail throughout execution (IDs, variables, remote calls, durations, errors) → emit the entire map as structured key-value pairs when the request exits or errors.
- **Wide event as documentation**: Instrumentation itself serves as the best documentation — combining intent (what dimensions the engineer decided to collect) with real-time production state.

## Key Concepts
- **Arbitrarily wide structured event**: A map of key-value pairs capturing everything relevant to one unit of work. "Arbitrarily wide" means no field limits.
- **Unit of work**: The scope for one event. Typically one HTTP request through one service. Events from the same request across multiple services are stitched into traces (Ch 6).
- **Metric (disambiguated)**: A scalar value with optional tags, pre-aggregated over a predefined time window. Represents the behavior of all requests in that period collapsed into one number.
- **Pre-aggregation problem**: Metrics collapse all individual requests into one number. You can never ask new questions of pre-aggregated data; the granularity is permanently lost.
- **Structured logs**: Logs redesigned as machine-parsable key-value pairs. Useful for observability if redesigned to represent a complete event (all fields for one unit of work) rather than multiple lines per event.
- **Unstructured logs**: Multiple human-readable lines per event; slow to search, hard to aggregate, not useful for high-cardinality analysis.

## Code Examples
```json
{
  "authority": "10.0.0.3:63349",
  "duration_ms": 123,
  "level": "info",
  "msg": "Served HTTP request",
  "path": "/super/slow/server",
  "port": 80,
  "service_name": "slowsvc",
  "status": 200,
  "time": "2019-08-22T11:57:03-07:00",
  "trace.trace_id": "eafdf3123",
  "user": "foo"
}
```
This demonstrates a complete event: one unit of work, structured, machine-parsable, including trace correlation.

## Anti-patterns
- **Using metrics as the primary debugging unit**: Pre-aggregated metrics can never be decomposed to show individual request behavior. Arms race of adding more metrics is both expensive and ultimately insufficient.
- **Multi-line unstructured logs**: One connection/request spread across 5 log lines with no common field is essentially useless for programmatic analysis.
- **Schema-constrained event stores**: Requiring predefined schemas forces engineers to predict future debugging dimensions — violating the fundamental observability requirement.

## Key Takeaways
1. Collect arbitrarily wide structured events, not pre-aggregated metrics, as your primary observability data type.
2. Mature instrumentation typically captures 300–400 dimensions per event.
3. Both high-cardinality (UUID-level uniqueness) and high-dimensionality (hundreds of fields) are required to find deeply hidden patterns.
4. Structured logs can approximate events if redesigned to capture one complete unit of work per entry.
5. No schema imposed at write time; schema is inferred at read time.

## Connects To
- **Ch 6**: Structured events are stitched together with trace IDs to form distributed traces.
- **Ch 7**: OpenTelemetry provides the instrumentation framework for generating these events.
- **Ch 16**: The data store must be designed to handle arbitrarily wide, schema-free events at high cardinality.
