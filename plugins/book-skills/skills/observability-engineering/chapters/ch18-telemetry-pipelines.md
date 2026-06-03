# Chapter 18: Telemetry Management with Pipelines

## Core Idea
A telemetry pipeline — a chain of receivers, buffers, processors, and exporters — decouples telemetry producers from backends, enabling routing, transformation, security enforcement, sampling, and enrichment without application changes. Essential at scale; useful for any organization needing routing or compliance control.

## Frameworks Introduced
- **Pipeline component model**:
  - **Receiver**: Collects data from sources (HTTP API, Prometheus scraper, Kafka consumer). Writes to buffer.
  - **Buffer**: Temporary store (Kafka, Kinesis, local disk). Smooths spikes, provides durability, enables replay.
  - **Processor**: Transforms messages (format conversion, PII filtering, enrichment, sampling). Reads from buffer, writes to buffer.
  - **Exporter (sink)**: Final destination writer (Elasticsearch, S3, Honeycomb, Prometheus). Reads from buffer.
  - Simple pipeline: `receiver → buffer → exporter`
  - Complex pipeline: `receiver → buffer → processor → buffer → exporter`

## Key Concepts
- **Pipeline attributes**: Routing, security/compliance, workload isolation, data buffering, capacity management (rate limiting, sampling, queuing), data filtering/augmentation, data transformation, data quality/consistency.
- **Workload isolation**: Separate high-volume and low-volume tenants into different clusters to prevent noisy-neighbor query degradation.
- **Data freshness SLO**: Slack sends 100 synthetic log messages/minute and queries them every 10 seconds to verify pipeline health. Freshness SLO measures whether all expected messages arrive within a given window.
- **SpanEvent (Slack)**: Simplified span format (ID, timestamp, duration, parent ID, trace ID, name, type, tags) making traces directly queryable in data warehouses and by big data tools (Presto, SQL joins).
- **Murron (Slack)**: In-house Go pipeline managing 120+ streams at several million messages/second, buffered through 200+ Kafka topics across 20 clusters.
- **Open source alternatives**: Fluent Bit (C, efficient), Vector (Rust, efficient), Cribl (Go), Logstash/Filebeat, Fluentd, rsyslog, OTel Collector.

## Reference Tables
| Pipeline component | Trace data operations | Metrics data operations | Logs data operations |
|-------------------|----------------------|------------------------|---------------------|
| Receiver | Gather in Zipkin/Jaeger/OTel formats | Scrape Prometheus targets | Collect from services |
| Exporter/Processor | Tail sampling, format conversion, routing for compliance | Relabeling, downsampling, aggregation, high-cardinality detection | PII filtering, GeoIP enrichment, routing to indexed vs. flat-file backends |

## Anti-patterns
- **Direct application-to-backend connections at scale**: Cannot reroute without application changes; no isolation or buffering.
- **Building pipeline from scratch**: Modern open source tools (Fluent Bit, Vector, OTel Collector) are purpose-built and well-maintained. Build only bespoke processing logic.
- **Ignoring data freshness monitoring**: Pipelines can degrade silently; synthetic data sources provide the only reliable freshness signal.
- **Colocating incompatible workloads**: High-volume log sources alongside low-volume critical sources without isolation causes availability issues for the low-volume data.

## Key Takeaways
1. Pipeline = receiver + buffer + processor + exporter. Each component is independently replaceable.
2. Kafka (or equivalent pub/sub) as the buffer enables durability, replay, and decoupling of producers from consumers.
3. Synthetic data sources are essential for pipeline health monitoring — without them, you cannot measure data freshness.
4. Data transformation to a common format (SpanEvent at Slack) enables multi-backend fan-out and data warehouse analytics.
5. Prefer open source components for the infrastructure; build only the bespoke processing logic your organization needs.

## Connects To
- **Ch 7**: OTel Collector is the standard receiver for OTLP data entering a pipeline.
- **Ch 17**: Collector-side tail sampling is a pipeline processor operation.
- **Ch 16**: The pipeline delivers data to the columnar store described in Ch 16.
