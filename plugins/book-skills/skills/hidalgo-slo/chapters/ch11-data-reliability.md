# Chapter 11: Data Reliability

## Core Idea
Data services have unique reliability properties where failures may be permanent and irreversible. Durability, integrity, and consistency failures persist forward indefinitely — unlike availability or latency failures, which are ephemeral once fixed.

## The 13 Data Properties

### 7 Data Properties (inherent to the data itself)

| Property | Definition | Example SLO |
|---|---|---|
| **Freshness** | How out-of-date is data vs. most recent source input? | "97% of data available in dashboard within 15 minutes of event" |
| **Completeness** | Does the dataset include all items representing an entity? | "99.9% of events counted during ingestion will be processed within 15 min" |
| **Consistency** | Do all stores agree on a particular datum? | "99.99% of reads across replicas return identical values for same key" |
| **Accuracy** | Does data correctly describe the entity or event? | "Fraud model training data: 99.5% records correctly labeled" |
| **Validity** | Does data conform to schemas or business rules? | "99.9% of ingested records pass schema validation" |
| **Integrity** | How trustworthy is data based on governance and traceability? | "100% of records have verified provenance chain" |
| **Durability** | How likely is there a known-healthy copy of the data? | "Annual probability of data loss < 0.0001%" |

### 6 Data Application Properties

| Property | Definition |
|---|---|
| **Security** | Protection from unauthorized access/alteration |
| **Availability** | Service continuity and response time/success rate |
| **Scalability** | Elasticity as data volume grows |
| **Performance** | Latency, throughput, efficiency |
| **Resilience** | Speed of recovery after disruption |
| **Robustness** | Handling of invalid inputs or stress conditions |

## Key Distinctions from Standard Service SLOs

- **Durability and integrity failures are permanent**: A lost file stays lost. A corrupted record corrupts derived data indefinitely. Recovery may be impossible.
- **Freshness ≠ age**: Week-old data can be fresh if it's the latest available; minute-old data can be stale if newer data was expected.
- **Completeness is hard to measure without a source of truth**: For provider-generated data with no external ground truth, audit counts at ingestion and compare to processed counts.

## Design Tensions

Data properties often conflict:
- **Completeness vs. Freshness**: Waiting for all records takes time; serving early means incomplete data.
- **Consistency vs. Availability**: (CAP theorem territory) Strong consistency requires coordination that reduces availability.
- **Durability vs. Performance**: Fsync-on-every-write is durable but slow.

SLOs help resolve these tensions by making the trade-offs explicit: "We prioritize freshness over completeness for this real-time dashboard."

## Data Lineage

Data lineage = tracking the provenance of data through processing stages. Critical for:
- Debugging accuracy and completeness failures (where in the pipeline did data change?)
- Meeting integrity requirements (can you prove data was not altered?)
- SLO violation attribution (which stage violated the freshness SLO?)

Implement by recording data interaction timestamps at each pipeline stage. This reveals the bottleneck component when freshness SLOs are violated.

## Example SLOs by Property

- **Freshness (weekly batch)**: "99% of data for the previous week is available for reporting by 09:00 each Monday."
- **Freshness (real-time)**: "97% of data is available in the dashboard within 15 minutes of an event."
- **Completeness (leaderboard)**: "99.99% of data updates result in 50 data rows with all fields present."
- **Completeness (pipeline)**: "99.9% of events counted during ingestion will be successfully processed and stored within 15 minutes of ingestion."

## Anti-patterns

- **Treating durability like availability**: "We'll fix it if it breaks" doesn't apply to permanent data loss.
- **Single-DC data storage for durable data**: Correlated failure modes mean co-located replicas don't provide true independence.
- **Ignoring data producers as users**: Metric forwarding clients, content uploaders, and data publishers are users with their own SLO requirements.
- **Optimizing only for one property**: Maximizing completeness while ignoring freshness may produce accurate but stale data that's worse than incomplete fresh data.

## Key Takeaways

1. Data reliability has 13 distinct properties; not all are relevant for every service.
2. Durability failures are permanent — invest disproportionately in prevention.
3. Freshness ≠ recency; define freshness relative to when new data was expected.
4. SLOs make data quality trade-offs explicit and actionable.
5. Data lineage is essential for debugging and attributing SLO violations in pipelines.

## Connects To

- **Ch 9**: Statistical durability calculations (independent replicas)
- **Ch 10**: Architectural decisions that affect data reliability
- **Ch 12**: Worked example includes a data pipeline SLO
