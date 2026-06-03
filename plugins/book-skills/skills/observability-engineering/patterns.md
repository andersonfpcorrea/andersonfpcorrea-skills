# Observability Engineering — Patterns & Techniques

## Core Analysis Loop
**When to use**: Any debugging session — alert response, customer complaint, or proactive investigation.
**How**: (1) Start with context from alert/customer. (2) Verify the change in performance visualized in data. (3) Group-by common fields, slice dimensions, filter for outliers. (4) Narrow scope, repeat from step 3 until root cause found.
**Trade-offs**: Requires arbitrarily wide structured events and a query tool that returns results in seconds. Cannot be done with pre-aggregated metrics.

## Wide Event Instrumentation
**When to use**: Any service instrumentation — start here before choosing a backend.
**How**: At request entry, initialize an empty map. Throughout execution, append: request parameters, user IDs, execution times, remote call durations, error messages, host/container info, business logic values. On exit/error, emit the full map as structured key-value pairs.
**Trade-offs**: Storage overhead proportional to event width and request volume. Mitigated by sampling (Ch 17).

## SLO-Based Alerting
**When to use**: Replacing threshold-based monitoring alerts for user-facing services.
**How**: Define SLI (good/bad event classification), set SLO target (e.g., 99.9% good over 30d), configure error budget burn alerts (both short-term and long-term lookahead), delete all potential-cause threshold alerts.
**Trade-offs**: Requires observability to make alerts debuggable. Cultural resistance when removing traditional alerts — run parallel until SLO alerts prove themselves (as Honeycomb did).

## Predictive Burn Alert (Proportional Extrapolation)
**When to use**: SLO burn alert configuration.
**How**: Use sliding 30-day window. Set lookahead:baseline ratio of 4:1. Configure multiple alerts (e.g., 1h baseline → 4h lookahead; 6h baseline → 24h lookahead). Use proportional extrapolation: if 50% of traffic in the last 6h failed, project 50% failure × expected 24h traffic volume.
**Trade-offs**: Context-aware burns (tracking cumulative budget) are more expensive. Watch for Lambda cost explosions on large data sets.

## OTel Auto + Custom Instrumentation
**When to use**: Any new service instrumentation.
**How**: (1) Add OTel auto-instrumentation wrappers for HTTP/gRPC (immediate skeleton of service topology). (2) Add custom spans for expensive code paths. (3) Add attributes for business logic values (customer ID, cart value, feature flags). (4) Export via OTLP to Collector or directly to backend.
**Trade-offs**: Auto-instrumentation is coarse; custom instrumentation takes ongoing effort but provides the most debugging value. OTel adds ~5-10ms overhead per span in Go.

## Consistent Trace Sampling
**When to use**: Any trace sampling implementation.
**How**: Generate a random Sampling-ID at the root span. Propagate it via HTTP header to all child services. Each service uses the same Sampling-ID value (not its own random number) to make the sampling decision. This ensures a sampled trace is sampled in its entirety.
**Trade-offs**: Requires adding sampling header propagation to all services. Misimplemented, you get partial traces.

## Per-Key Target Rate Sampling
**When to use**: High-volume services where constant sampling would miss rare errors.
**How**: Identify 2-3 sampling keys (e.g., [error_message, latency_bucket, customer_tier]). Track count per key in a rolling window. Compute sample rate per key as `count / (window_seconds * target_per_second)`. Apply different rates to normal vs. outlier events (e.g., 1/1000 for success, 1/1 for errors).
**Trade-offs**: Complexity grows with number of keys. Use dynsampler-go or similar library rather than implementing from scratch.

## Telemetry Pipeline (Receiver-Buffer-Processor-Exporter)
**When to use**: Multiple telemetry backends, compliance requirements, workload isolation, high volume (100M+ events/day).
**How**: Deploy receiver per data type (OTLP for traces, Prometheus scraper for metrics, log shipper). Buffer via Kafka (3-day retention for replay). Add processors for PII filtering, format conversion, enrichment. Configure exporters per backend.
**Trade-offs**: Adds operational complexity and latency. At smaller scales, direct application-to-backend connections are simpler. Use open source components (OTel Collector, Fluent Bit, Vector) rather than building from scratch.

## Buy-and-Build Observability Team
**When to use**: Organizations >50 engineers considering observability tool investment.
**How**: Buy a commercial observability backend with strong APIs. Create an internal observability team (3-5 engineers) whose job is NOT to build the core tool but to: write instrumentation libraries, standardize naming conventions, manage vendor relationship, consult with product teams on instrumentation, evaluate new tools as needs evolve.
**Trade-offs**: Requires PM function to prioritize internal tooling needs. Risk of low adoption if UX isn't streamlined for non-experts.

## Pain-First Adoption
**When to use**: Starting a new observability initiative.
**How**: Identify the most elusive, most painful production problem (the service that wakes people up). Instrument it with OTel. Demonstrate value by solving the hard problem publicly. Use success as social proof to drive broader adoption.
**Trade-offs**: Starting with a hard problem increases failure risk. Mitigated by: buying proven tooling, using OTel, and iterating instrumentation rather than perfecting it before launch.

## Synthetic Data Freshness Monitoring (Pipeline Health)
**When to use**: Any telemetry pipeline operating in production.
**How**: Emit N synthetic events per minute from multiple pipeline ingress points with unique identifiable values. Query for these synthetic events every 10–30 seconds. Measure what percentage of expected events arrive within a given time window. Alert when percentage drops below SLO.
**Trade-offs**: Synthetic data must be filtered from user queries. Monitoring the pipeline health is itself instrumentation work.
