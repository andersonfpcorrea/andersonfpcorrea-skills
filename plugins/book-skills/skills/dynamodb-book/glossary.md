# Glossary — The DynamoDB Book

Alphabetical reference for every significant term, concept, and pattern named in the book.

**A**

**Access pattern** — A specific read or write the application must perform; enumerated upfront because every primary key and index is designed to serve one (Ch 7).
**Access pattern chart** — Tabular artifact (Entity, Pattern, Index, Parameters, Notes) listing every required read/write before keys are designed (Ch 7).
**Adaptive capacity** — Auto-redistribution of throughput across partitions that mitigates hot-partition throttling, but cannot save low-cardinality partition keys (Ch 3).
**Adjacency list** — Many-to-many strategy where the relationship itself is an item co-located with one parent on the base table and with the other parent in an inverted GSI (Ch 12, 22).
**API-centric pattern gathering** — Listing each REST endpoint and its response shape to enumerate access patterns when building an API (Ch 7).
**Application attribute** — Business-meaningful attribute (e.g., `Username`); kept distinct from indexing attributes and never derived from keys (Ch 9).
**Ascending order** — Default sort direction (`ScanIndexForward=True`); fake it within a descending collection using the zero-padded difference trick (Ch 14).
**`attribute_exists()` / `attribute_not_exists()`** — Condition-expression functions used for existence and uniqueness guards on writes (Ch 6, 16).
**Atomic counter** — Race-safe numeric increment via `SET x = x + :incr` in an UpdateExpression (Ch 6, 16).
**Attribute** — A typed value on an item; not required across all items in a table (Ch 2).
**Attribute name abbreviation** — Storing short names (`u`, `fn`) instead of full names to save bytes; only worth it at billions of items (Ch 9).
**Auto-scaling** — Provisioned-capacity feature that adjusts RCU/WCU within configured bounds (Ch 1).

**B**

**Backfill** — Adding new indexing attributes to existing items via a Scan + UpdateItem ETL job (Ch 15, 22).
**BatchGetItem** — Item-based action that fetches up to 100 items in one round trip with per-item success/failure (Ch 4).
**BatchWriteItem** — Item-based action for up to 25 puts/deletes in one round trip with per-item success/failure (Ch 4).
**`begins_with()`** — Sort-key function allowed in KeyConditionExpression; also a condition function for filters (Ch 4, 6).
**BETWEEN** — Sort-key operator that subsumes all other range conditions; DeBrie's default (Ch 6).
**Boundary implementation pattern** — Confining all DynamoDB-specific code (keys, expressions, type conversion) to a single data-access module at the app edge (Ch 9).
**Burst capacity** — Reserve of unused provisioned capacity DynamoDB lets you spend in short bursts (Ch 1).

**C**

**Client-side filtering** — Fetching a small dataset and filtering in the browser/app; legitimate for small datasets with flexible UI filters (Ch 13).
**Composite primary key** — A primary key made of partition key + sort key; required for any Query and for fetch-many access patterns (Ch 2, 7).
**Composite sort key** — A sort key VALUE that concatenates multiple data elements (e.g., `STATUS#DATE`) to enable multi-attribute filtering or hierarchical `begins_with` (Ch 11, 13).
**ConditionCheck** — A TransactWriteItems action that asserts an item's state without writing (Ch 6).
**ConditionExpression** — Pre-write assertion that aborts the write if false; replaces read-then-write patterns and prevents races (Ch 6).
**ConsistentRead** — Optional flag (GetItem, BatchGetItem, Query, Scan on base/LSI) for strongly consistent reads at 2× RCU cost; not allowed on GSIs (Ch 5).
**`contains()`** — Condition-expression function for substring or set-membership checks; NOT allowed in KeyConditionExpression (Ch 4, 6).

**D**

**DAX** — DynamoDB Accelerator, an in-memory cache fronting DynamoDB for sub-millisecond reads (Ch 1).
**Debug script** — Small CLI wrapping the data layer to exercise one access pattern from the terminal (Ch 9).
**DeleteItem** — Item-based action that removes a single item by full primary key (Ch 4).
**Denormalization** — Deliberate data duplication to pre-join data for single-Query access; the core DynamoDB modeling habit (Ch 7, 11).
**Descending order** — `ScanIndexForward=False` on Query; mandatory for "most recent N" patterns (Ch 5).
**Document Client / Boto3 Table resource** — Thin SDK helpers that auto-infer attribute types; the only acceptable abstraction above the raw API (Ch 5).
**DynamoDB Streams** — Built-in immutable, ordered change-data-capture log of every write, consumed by Lambda for fan-out (Ch 3, 20).

**E**

**Entity** — A distinct domain object type modeled in the ERD (e.g., User, Order) (Ch 7).
**Entity chart** — Table (Entity, PK template, SK template) used while designing primary key shapes per entity type (Ch 7).
**ERD (Entity-Relationship Diagram)** — Boxes-and-lines diagram of entities and cardinalities; DeBrie omits attributes to stay focused on relationships (Ch 7, 17).
**ETL job** — Scan-filter-UpdateItem loop for any non-trivial backfill migration (Ch 15, 22).
**Eventually consistent reads** — Default read mode; may return slightly stale data from a secondary replica (Ch 3).
**ExpressionAttributeNames** (`#name`) — Aliases for attribute references; required for reserved words and nested attributes (Ch 5, 6).
**ExpressionAttributeValues** (`:val`) — Typed value bindings for expressions; required everywhere except ProjectionExpression (Ch 5, 6).

**F**

**Faux-SQL** — Using DynamoDB with normalized multi-table tables in a relational style; trades single-table perf for flexibility (Ch 8).
**FilterExpression** — Post-fetch filter for Query/Scan; runs AFTER the 1MB read so it does NOT save RCU (Ch 6, 13).
**Five misconceptions checklist** — DeBrie's pre-rejection test (just K-V, can't scale, only hyperscale, can't migrate, no schema needed) (Ch 1).
**Five-step modeling process** — Understand app → ERD → access patterns → primary key → secondary indexes/streams (Ch 7).

**G**

**Generic key naming** — Using `PK`, `SK`, `GSI1PK` instead of descriptive names so multiple entity types can share keys (Ch 7, 9).
**GetItem** — Item-based action that fetches a single item by full primary key (Ch 4).
**Global Tables** — Multi-region active-active replication of a DynamoDB table (Ch 1).
**GSI (Global Secondary Index)** — Secondary index with any attributes as keys, addable anytime, eventually consistent only, with its own throughput (Ch 2).
**GSI overloading** — Using one GSI's keys (`GSI1PK`/`GSI1SK`) to serve multiple access patterns across different entity types (Ch 7, 11).

**H**

**Hot partition** — Partition receiving >3000 RCU or >1000 WCU per second; must be sharded or restructured (Ch 3).
**HTTP connection model** — Stateless request model with no connection pool, enabling unlimited concurrency (notably from Lambda) (Ch 1).

**I**

**IAM authentication** — AWS Identity and Access Management replaces DB users/passwords; can authorize down to actions, keys, or attributes (Ch 1).
**Indexing attribute** — Attribute that exists only to power a primary key or secondary index; carries no business meaning (Ch 9).
**Inverted index** — A GSI where the base table's PK and SK are flipped to expose the other side of a relationship (Ch 12).
**Item** — A single record in a table, roughly equivalent to a row or document (Ch 2).
**Item collection** — All items in a table or index sharing one partition key; the unit a Query operates on (Ch 2, 8).

**K**

**KEYS_ONLY projection** — GSI projection type that copies only the key attributes; minimizes index storage and write cost (Ch 18).
**Key overloading** — Storing multiple entity types under one table by using generic key names with prefixed values (e.g., `ORG#`, `USER#`) (Ch 3).
**KeyConditionExpression** — Query-only expression on partition key (equality required) and sort key (range operators allowed); the only expression that reduces items physically read (Ch 4, 6).
**KSUID** — 27-character time-prefixed sortable unique identifier; preferred over UUIDv4 when chronological sort matters (Ch 14).

**L**

**LastEvaluatedKey** — Cursor token returned on truncated Query/Scan responses; passed back as `ExclusiveStartKey` for pagination (Ch 4, 16).
**Lazy migration** — Adding a new application attribute by writing it on next update with no ETL (Ch 22).
**Lexicographic order** — Byte-wise UTF-8 sort that puts uppercase before lowercase and `"10"` before `"2"`; drives all string sort-key design (Ch 14).
**LSI (Local Secondary Index)** — Secondary index sharing the base table's partition key with a different sort key; create-time only; supports strongly consistent reads; bound to the 10GB item-collection limit (Ch 2).

**M**

**Materialized graph** — Many-to-many strategy modeling each node as an item collection with edge-shaped GSIs; niche, used for knowledge/social graphs (Ch 12).
**Migration ladder** — DeBrie's difficulty ranking from easiest (new attribute) to hardest (refactor existing access pattern) (Ch 22).

**N**

**Normalization (1NF/2NF/3NF)** — Relational forms intentionally violated by DynamoDB denormalization (Ch 7).
**Normalization & multiple requests** — Many-to-many fallback strategy storing sparse link items and hydrating via Query + BatchGetItem (Ch 12).

**O**

**OLTP** — Online transaction processing — fast small reads/writes; DynamoDB's sweet spot, as opposed to OLAP analytics (Ch 1, 8).
**On-demand capacity** — Pay-per-request pricing with no planning; higher per-unit cost than provisioned (Ch 1).
**Overloaded primary key** — Same `PK`/`SK` attributes carrying different value patterns per entity type (Ch 8).

**P**

**Pagination** — Cursor-based traversal using `LastEvaluatedKey` / `ExclusiveStartKey`; DynamoDB has no OFFSET (Ch 16).
**Parallel scan** — Splitting a Scan via `TotalSegments` and `Segment` so multiple workers read the table concurrently (Ch 15).
**Parent-in-the-middle** — Sort strategy that places the parent item between two child types so one Query (in either direction) returns parent + children (Ch 14, 21).
**Partition** — A ~10GB storage node holding items hashed to a key range, replicated as 1 primary + 2 secondaries (Ch 3).
**Partition key (PK)** — Also called hash key; hashed to locate the storage node and required on every item-based action and Query (Ch 2).
**Point-in-time recovery (PITR)** — Continuous backup feature allowing restore to any second in the last 35 days (Ch 1).
**Pre-joining** — Designing partition keys so heterogeneous items that must be read together already share an item collection (Ch 8).
**Primary key** — Either a simple partition key or a composite partition + sort key; uniquely identifies every item (Ch 2).
**ProjectionExpression** — Read-side expression limiting which attributes return; reduces bandwidth but not read cost (Ch 6).
**Provisioned capacity** — Pre-allocated RCU/WCU; cheaper per unit but requires capacity planning (Ch 1).
**PutItem** — Item-based action that writes (or overwrites) a single item by full primary key (Ch 4).

**Q**

**Query** — API action that reads one item collection by partition key with optional sort-key conditions; bounded by 1MB per call (Ch 4).

**R**

**RCU (Read Capacity Unit)** — 1 strongly-consistent or 2 eventually-consistent reads per second up to 4KB (Ch 1).
**Read-shard cache** — Duplicating a hot payload across N shard items inside DynamoDB so reads pick a random shard (Ch 20).
**Reference count** — Denormalized counter on the parent updated atomically with child writes via a transaction (Ch 16, 21).
**Relationship item** — A first-class item representing an edge (e.g., `Role`, `Following`, `Membership`) in a many-to-many model (Ch 12).
**Reserved words** — 573 DynamoDB-reserved attribute names; aliased via ExpressionAttributeNames to avoid collisions (Ch 5).
**ReturnConsumedCapacity** — Request flag (`NONE`/`TOTAL`/`INDEXES`) surfacing the billed RCU/WCU for a call (Ch 5).
**ReturnItemCollectionMetrics** — Write-API flag returning approximate collection size; early-warning tripwire for the 10GB LSI limit (Ch 5).
**ReturnValues** — Write-API option (`NONE`/`ALL_OLD`/`UPDATED_OLD`/`ALL_NEW`/`UPDATED_NEW`) folding a read into a write (Ch 5).

**S**

**Scan** — API action that reads every item in a table or index; avoid in production access paths (Ch 4).
**ScanIndexForward** — Query-only boolean; `False` reads sort key descending — essential for "most recent N" (Ch 5).
**Schemaless** — DynamoDB enforces no schema; the application must (Ch 1).
**Secondary index** — A reshape of table data with a new key schema (LSI or GSI) enabling additional access patterns (Ch 2).
**Sequential IDs** — Human-friendly numeric IDs emulated via atomic counter on a parent plus follow-up Put (Ch 16, 21).
**Shallow duplication** — Many-to-many strategy embedding a small immutable subset (IDs and names) of one side on the parent (Ch 12).
**Single-table design** — Storing all entity types of an application in one table with overloaded keys to serve heterogeneous reads in one Query (Ch 8).
**Singleton item** — Item with a fixed PK/SK holding global state (curated lists, toggles, counts) (Ch 16, 20).
**`size()`** — Condition-expression function for length/cardinality assertions (e.g., cap a set at 10) (Ch 6).
**Sort key (SK)** — Also called range key; orders items within a partition and enables range queries (Ch 2).
**Sparse index** — A GSI that excludes items lacking its key attributes — used for global filters on a rare subset or to project a single entity type (Ch 13, 20).
**Strategy** — A named, reusable modeling recipe with documented tradeoffs (Ch 10).
**Strongly consistent reads** — Reads reflecting all prior writes; opt-in with `ConsistentRead=True` at 2× RCU; unavailable on GSIs (Ch 3).

**T**

**Table** — A grouping of items; in single-table design holds many entity types (Ch 2).
**Time-bucket sharding** — Partitioning a high-volume "latest X" stream by a truncated timestamp to bound partition size (Ch 20).
**Tracking item** — A first-class item with no business data, used to enforce uniqueness on a non-PK attribute (Ch 16, 19).
**TransactGetItems** — Reads up to 25 items as a single atomic transaction (Ch 4).
**TransactWriteItems** — Writes up to 25 items all-or-nothing, including ConditionCheck-only entries (Ch 4, 16).
**TTL (Time To Live)** — Per-item Unix-timestamp attribute that DynamoDB lazily deletes within ~48 hours; never trust for correctness, verify on read (Ch 3, 18).
**Two-axis fit test** — Use DynamoDB when EITHER hyper-scale OR hyper-ephemeral compute (Lambda) applies (Ch 1).
**Type attribute** — String attribute on every item naming its entity type; pays off during migrations and analytics (Ch 9).

**U**

**UI-centric pattern gathering** — Walking each screen/URL and listing data needed to render it; alternative to API-centric enumeration (Ch 7).
**UpdateExpression** — Mutation grammar using SET, REMOVE, ADD, DELETE verbs on UpdateItem (Ch 6).
**UpdateItem** — Item-based action that mutates a single item by full primary key (Ch 4).

**W**

**WCU (Write Capacity Unit)** — 1 write per second up to 1KB (Ch 1).
**Wide-column store** — DynamoDB's underlying model: a hash table of B-trees ("bookshelf of phone books") (Ch 1).

**Z**

**Zero-padded difference** — Storing `MAX - n` zero-padded in a sort key to invert numeric sort direction in place; enables two child types reading the same direction off one parent (Ch 14, 22).
**Zero-padding** — Left-padding integers in string sort keys to a fixed width so lexicographic order matches numeric order (Ch 14).

**4 Numbers**

**400 KB item limit** — Maximum size of a single item; caps unbounded denormalization (Ch 3).
**1 MB Query/Scan response limit** — Per-request response ceiling evaluated BEFORE FilterExpression (Ch 3, 6).
**10 GB LSI item-collection limit** — Hard write rejection at the limit; no equivalent for GSI-only tables (Ch 3).
**3000 RCU / 1000 WCU per-partition limit** — Hot-partition ceiling that triggers the need to shard (Ch 3).
