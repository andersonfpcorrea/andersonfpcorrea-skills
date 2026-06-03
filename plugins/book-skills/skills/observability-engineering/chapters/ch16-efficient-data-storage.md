# Chapter 16: Efficient Data Storage

## Core Idea
Observability workloads require a purpose-built data store: queries must return in seconds, any field must be queryable ad hoc, data must be available within seconds of ingestion, and no dimension can be privileged over others. TSDBs and standard NoSQL databases are inadequate. A hybrid columnar store segmented by time is the solution.

## Frameworks Introduced
- **Retriever architecture (Honeycomb's columnar store)**:
  1. **Time-partitioned segments**: Incoming spans appended to active segment per tenant. Segment finalized when 1h elapsed OR >250K records OR >1GB. Segment stores min/max timestamp metadata.
  2. **Per-column files within segments**: One append-only file per field per segment + timestamp index. Enables reading only relevant columns for any query.
  3. **Dictionary/compression**: Run-length encoding, sparse encoding, LZ4 compression reduce storage and I/O.
  4. **Parallelism**: Segments processed independently (map-reduce style via serverless Lambda); results merged. Impatience pattern: retry slowest 10% when 90% of requests complete.
  5. **Tiering**: Recent data on local SSD; older data archived to S3; fetched on demand for historical queries.
  6. **Durability**: Apache Kafka for ordered, durable data buffering. Stateless receivers → Kafka → stateful indexing workers. Dual consumers per partition for redundancy.

## Key Concepts
- **Functional requirements for observability data stores**: (1) Query results in seconds; (2) All fields queryable ad hoc, no pre-declared indexes; (3) Data available to query within seconds of ingestion; (4) Fault-tolerant; (5) Durable.
- **TSDB cardinality explosion**: Adding a high-cardinality field like `user_id` creates a new time series per unique value → linear overhead with number of events → prohibitive.
- **Row-based storage (Bigtable model)**: Fast individual row retrieval; expensive arbitrary cross-row analysis; compaction overhead for write-once-read-many workloads.
- **Column-based storage (Dremel/ColumnIO model)**: Efficient partial column scans; row reconstruction requires reading all column files; no row ordering guarantee.
- **Hybrid columnar approach**: Time-partition (segments) provides row locality by time; columnar within segments enables partial column reads.

## Reference Tables
```
Query workload (6 steps):
1. Identify segments overlapping query time range.
2. For each segment: scan column files for filter (WHERE) and output (SELECT/GROUP) columns.
3. Evaluate timestamp of each row; skip rows outside time range.
4. Aggregate within segment (COUNT, SUM, MAX, etc.).
5. Aggregate across segments (merge by GROUP key).
6. Sort groups, pick top-K.

Retriever scale (Nov 2021):
- ~1,000 vCPU receiver workers
- 100 vCPU Kafka brokers
- ~1,000 vCPU ingest + query workers
- 30,000 peak concurrent Lambda executions
- ~700 TB columnar data
- 500M compressed segment archives
- 1.5M trace spans/second ingestion
- Max lag: milliseconds until queryable
- Median query latency: 50ms; p99: 5 seconds
```

## Anti-patterns
- **TSDBs for observability events**: Cardinality explosion makes high-cardinality field storage prohibitively expensive.
- **NoSQL databases (MongoDB, Snowflake)**: Good for ingestion; slow for arbitrary multi-dimension queries without pre-indexing; pre-indexing defeats the purpose.
- **Elasticsearch/Cassandra for observability**: Not purpose-built for this workload; operational complexity without the performance benefits.
- **Pre-indexing all columns**: Index size approaches or exceeds data size (Google Dapper: 3-field index was 76% of trace data size).

## Key Takeaways
1. Time is the only privileged dimension; all other fields must be equally fast to query.
2. A hybrid columnar store segmented by time satisfies both the full-row trace lookup and cross-row analytical query requirements.
3. Append-only segments with start/end timestamp metadata eliminate sorting overhead at write time.
4. Map-reduce parallelism + cloud object storage (S3) + serverless compute enables sub-second queries over terabytes.
5. Alternatives worth evaluating: Google BigQuery, ClickHouse, Apache Druid — all can handle observability workloads but may require custom sharding work.

## Connects To
- **Ch 5**: Arbitrarily wide events are the input; the storage system must accommodate schema-free, high-cardinality data.
- **Ch 13**: Event-based SLI calculation requires per-request granularity — impossible with a TSDB.
- **Ch 17**: Sampling reduces the volume that must be stored without losing representativeness.
