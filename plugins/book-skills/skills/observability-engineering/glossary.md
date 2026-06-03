# Observability Engineering — Glossary

**AIOps** — Artificial intelligence for operations; algorithms for anomaly detection and alert noise reduction. Authors argue AIOps cannot reliably define baselines in rapidly changing production. (Ch 8)

**Alert fatigue** — Gradual desensitization to alerts caused by high false-positive rates; leads to normalization of deviance and missed real failures. (Ch 12)

**Arbitrarily wide structured event** — A map of key-value pairs capturing everything relevant to one unit of work, with no field-count limit. The fundamental data type of observability. (Ch 5)

**BubbleUp** — Automated core analysis loop: select an anomalous region in a heatmap → compute dimension values inside vs. outside → sort by percent difference. (Ch 8)

**Cardinality** — Uniqueness of values in a data set. High cardinality (UUIDs, user IDs) is most useful for debugging; metrics systems cannot handle high cardinality at scale. (Ch 1)

**Context propagation** — Passing trace/span IDs downstream via HTTP headers (W3C TraceContext, B3) so child spans can record their relationship to parent spans. (Ch 6)

**Core analysis loop** — 4-step systematic debugging workflow: (1) context, (2) verify change, (3) search dimensions, (4) done or filter and repeat. Enables debugging from first principles. (Ch 8)

**Dimensionality** — Number of keys in telemetry data. Wide events with hundreds of dimensions enable finding deeply hidden correlations. (Ch 1)

**Error budget** — Maximum allowed "bad" events derived from an SLO. Example: 99.9% SLO → 0.1% error budget. (Ch 12, 13)

**Explorability** — Ability to ask any question and inspect internal state iteratively without predicting the question in advance. One of the "real three pillars." (Ch 1)

**Head-based sampling** — Sampling decision made at trace initiation, propagated downstream. Use when criteria are known at request start. (Ch 17)

**Known-unknowns** — Failure modes you know can occur; what monitoring is designed to detect. (Ch 1)

**Murron** — Slack's in-house Go pipeline managing 120+ streams at millions of messages/second. (Ch 18)

**Normalization of deviance** — Accepting deviations from expected behavior as normal; coined during Challenger investigation; precursor to catastrophic failure. (Ch 12)

**Observability (definition)** — "A measure of how well you can understand and explain any state your system can get into, no matter how novel or bizarre, without needing to ship new code." (Ch 1)

**Observability Maturity Model (OMM)** — Framework measuring organizational progress across 5 capabilities: resilience, code quality, complexity management, release cadence, user behavior understanding. (Ch 21)

**Observability-driven development (ODD)** — Instrumentation bundled with every feature; validates code behavior in production under real load, not just against isolated specifications. (Ch 11)

**OpenTelemetry (OTel)** — CNCF open source standard for vendor-neutral application instrumentation; merger of OpenTracing and OpenCensus (2019). (Ch 7)

**OTLP (OpenTelemetry Protocol)** — Default wire protocol for OTel data; recommended for all OTel exporter configurations. (Ch 7)

**Potential-cause alerts** — Alerts triggering on underlying system conditions (CPU, memory) that may or may not indicate user impact; high false-positive rate. (Ch 12)

**Pre-aggregation problem** — Metrics collapse all individual requests into one number at write time; prevents asking new questions post-hoc. (Ch 5)

**Progressive delivery** — Controlled, partial deployment techniques (canarying, feature flags, blue/green, rolling deploys). Requires observability. (Ch 3, 4)

**Retriever** — Honeycomb's proprietary hybrid columnar data store; time-partitioned segments, per-column files, S3 tiering, Lambda parallelism. (Ch 16)

**Root span** — The top-level span in a trace; identified by the absence of a parent ID. (Ch 6)

**Sampling** — Selecting representative events to transmit rather than all events. Preserves cardinality on selected events; reduces cost. (Ch 17)

**Scuba** — Facebook's in-memory distributed analytics system enabling real-time arbitrary-dimension slice-and-dice; inspired the observability approach. (Ch 3)

**Service Level Agreement (SLA)** — External reliability commitment to customers. SLOs are typically more stringent internal targets. (Ch 12)

**Service Level Indicator (SLI)** — Measurement classifying each event or time window as "good" or "bad." (Ch 12)

**Service Level Objective (SLO)** — Internal reliability target; example: 99.9% of events good over 30-day window. (Ch 12)

**Sliding window** — SLO measurement over a rolling period (e.g., trailing 30 days) rather than fixed calendar period. Preferred; better reflects human memory of reliability. (Ch 13)

**SpanEvent** — Slack's simplified span format: ID, timestamp, duration, parent ID, trace ID, name, type, tags. Easier to produce and query than OTel spans. (Ch 18)

**Symptom-based alerting** — Alerts triggering when user experience is actually degraded; high signal-to-noise ratio. Used by SLO-based systems. (Ch 12)

**Tail-based sampling** — Sampling decision made after trace completion; requires buffering. Enables criteria based on outcome (latency, error status). (Ch 17)

**Tail-based sampling (buffered)** — Collector-side sampling; defers decision until full trace is assembled. (Ch 17)

**Telemetry pipeline** — Chain of receiver + buffer + processor + exporter components decoupling telemetry producers from backends. (Ch 18)

**Time-series database (TSDB)** — Database optimized for aggregated metrics over time. Incompatible with observability workloads due to cardinality explosion. (Ch 16)

**Unknown-unknowns** — Failure modes you could never have predicted; what observability is designed to detect. (Ch 1)

**Unit of work** — The scope for one structured event. Typically one HTTP request through one service. (Ch 5)
