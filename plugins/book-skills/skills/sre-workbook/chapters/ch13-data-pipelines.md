# Chapter 13: Data Processing Pipelines

## Core Idea
Data pipelines require the same SRE rigor as online services — SLOs, dependency planning, production readiness reviews — but with pipeline-specific SLIs (freshness, correctness, coverage) and failure modes (data loss, reprocessing cost, late data).

## Frameworks Introduced
- **Pipeline SLI types** (from Ch 2, applied here in depth):
  - Freshness: data updated within time threshold
  - Correctness: % records with correct output
  - Coverage: % jobs processing ≥ target data volume
  - Throughput: records processed per unit time

- **Pipeline production readiness criteria** (before going to prod):
  - SLOs defined for freshness, correctness, coverage
  - Dependency failure handling documented (what happens when upstream is down?)
  - Data lineage documented
  - Runbooks for common failure modes
  - Autoscaling configured and tested
  - Escalation paths defined

- **Idempotent and two-phase mutations**:
  - All writes should be idempotent: re-running a failed stage produces the same result without duplication.
  - Two-phase commit (write to temp location, then swap) makes large writes atomic and reversible.
  - Checkpointing: record the last successfully processed record so reruns start from there, not from the beginning.

## Key Concepts
- **Pipeline failure modes**: data loss, duplicate records, incorrect transformations, stale outputs, cascading backpressure.
- **Hotspotting**: uneven data distribution causes some workers to be overloaded while others are idle. Design sharding keys to distribute load evenly.
- **Backpressure**: when a downstream stage can't keep up, the upstream stage must queue or shed load. Plan for this explicitly.
- **Schema evolution**: pipelines that assume a fixed schema break when producers change their output. Use schema registries and backward-compatible evolution.

## Pipeline Best Practices (8 key ones)
1. Define and measure SLOs (freshness + correctness + coverage)
2. Plan for dependency failure: circuit breakers, fallback data sources
3. Create and maintain pipeline documentation (data lineage, ownership)
4. Map development lifecycle (how does a schema change get deployed safely?)
5. Reduce hotspotting and uneven workload patterns
6. Implement autoscaling and resource planning
7. Adhere to access control and security policies
8. Plan escalation paths (who owns the pipeline? Who's on-call?)

## Spotify Case Study: Event Delivery Pipeline
- Collects hundreds of billions of events per day
- Multi-stage: ingest → buffer (Kafka) → process → deliver to sinks (BigQuery, internal stores)
- SLOs: < 1-minute latency for 99% of events; < 0.01% event loss
- Key lesson: Google Cloud Pub/Sub + BigQuery simplified operations vs. managing infrastructure; chose managed services to reduce toil.
- Failure mode: replay from Kafka on downstream outages; events are not lost, just delayed.

## Anti-patterns
- **No SLO on the pipeline**: treating batch jobs as "best effort" makes it impossible to detect when data is significantly late.
- **Non-idempotent writes**: a retry after partial failure creates duplicate records that corrupt downstream analytics.
- **Manual schema changes**: pipeline schemas changed without coordination between producers and consumers cause data loss.

## Key Takeaways
1. Pipelines need SLOs; use freshness + correctness + coverage as the baseline SLI set.
2. All writes must be idempotent and checkpointed; plan for reprocessing from checkpoint.
3. Design for dependency failure: upstream data being late is normal, not exceptional.
4. Managed services (Pub/Sub, Dataflow, BigQuery) reduce pipeline toil significantly.

## Connects To
- **Ch 2**: Pipeline SLI types (freshness, correctness, coverage) defined there
- **Ch 6**: Pipeline toil (manual reruns, schema migrations) and how to eliminate it
