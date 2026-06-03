# Chapter 2: Core Concepts in DynamoDB

## Core Idea
DynamoDB is built from five primitives — tables, items, attributes, primary keys, secondary indexes — and almost all data modeling is the art of designing primary keys and secondary indexes that produce the right item collections for your access patterns.

## Frameworks Introduced
- **Primary key selection rule**: Choose simple primary key for pure one-to-one access; choose composite (partition + sort) for any "fetch many" access pattern.
- **Secondary index default**: Prefer Global Secondary Index (GSI) unless you specifically need strongly-consistent reads on an alternate sort key — then use LSI.

## Key Concepts
- **Table**: A grouping of items. Unlike RDBMS, often holds multiple entity types (to avoid joins).
- **Item**: A single record (≈ row / document).
- **Attribute**: A typed value on an item. Not required across items.
- **Primary key**: Either a single partition key (simple) or partition key + sort key (composite). Must be present on every item; uniquely identifies an item.
- **Partition key (a.k.a. hash key)**: Hashed to determine the storage node.
- **Sort key (a.k.a. range key)**: Orders items within a partition; enables range queries.
- **Secondary index**: A reshape of the table's data with a new key schema for additional access patterns.
- **LSI (Local Secondary Index)**: Same partition key as base table, different sort key. Must be created at table-creation time. Supports strongly-consistent reads.
- **GSI (Global Secondary Index)**: Any attributes as keys. Can be added/dropped anytime. Eventually consistent only. Has its own throughput.
- **Item collection**: All items in a table (or index) sharing the same partition key. The Query API operates on a single collection.
- **Attribute types**: Scalars (string, number, binary, boolean, null), complex (list, map), sets (string set, number set, binary set).

## Mental Models
- Think of an item collection as a "phone book" you can scan a contiguous range of — design it deliberately to serve a specific access pattern.
- Use the partition key as the "top-level filter" and the sort key as a "more granular filter or ordering dimension".
- Treat secondary indexes as alternate views, not duplicates — each one buys you a new query shape.
- Sets are for tracking uniqueness without a round trip; maps/lists for denormalized nested data.

## Anti-patterns
- **Putting only one entity type per table** (relational thinking): forces joins DDB can't do.
- **Choosing an LSI when you don't need strong consistency**: locks you into table-creation-time decisions and same-partition-key constraint.
- **Treating attributes like relational columns** that must exist on every item.
- **Picking a primary key without first writing down access patterns**: the most common DDB modeling error.

## Reference Tables

| | Key schema | Creation time | Consistency |
|---|---|---|---|
| **Local Secondary Index (LSI)** | Must use same partition key as base table | Must be created when table is created | Eventual by default; can request strongly-consistent reads at higher throughput cost |
| **Global Secondary Index (GSI)** | May use any attribute as partition and sort keys | Can be created after the table exists | Eventual consistency only |

| Attribute category | Types | Use for |
|---|---|---|
| Scalar | string, number, binary, boolean, null | Most fields |
| Complex | list, map | Denormalized nested data |
| Set | string set, number set, binary set | Tracking uniqueness |

## Key Takeaways
1. Primary key design is the single most important decision in DynamoDB modeling.
2. Composite primary keys unlock the Query API ("fetch many") — simple keys cannot.
3. Default to GSI; reach for LSI only when you must have strong consistency on an alternate sort key.
4. Item collections are the unit of efficient retrieval — design them on purpose.
5. Multiple entity types per table is a feature, not a bug; it replaces joins.
6. Schemaless at the DB level means schema enforcement is your application's job.

## Connects To
- **Ch 3**: Partitions store item collections; understanding partitions explains why item-collection design matters.
- **Ch 4**: The Query API consumes a single item collection — Ch 2's framing is what makes Ch 4's API meaningful.
- **Ch 7-8**: Single-table design builds directly on item-collection thinking introduced here.
