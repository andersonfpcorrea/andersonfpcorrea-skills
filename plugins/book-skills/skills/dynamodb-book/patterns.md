# DynamoDB Patterns & Strategies — DeBrie

A catalog of every named technique from the book. Each entry: when to use, how it works, tradeoffs.

## Modeling Approach

### Access-patterns-first design
**When to use**: Always — before any primary key is drawn. The #1 highest-leverage habit in the book.
**How**: Enumerate every read/write the app will issue (with parameters) in an Access Pattern Chart BEFORE choosing keys. Use the API-centric flavor for REST backends, UI-centric flavor for SSR/BFF backends.
**Tradeoffs**: Front-loads design effort; punishes "future flexibility" guessing. Without it, you ship the wrong PK and pay in ETL migrations.
**Source**: Ch 7, 10, 17.

### DeBrie's 5-step modeling process
**When to use**: Every new DynamoDB application.
**How**: (1) Understand the app, (2) draw an ERD with no attributes, (3) write every access pattern in a chart, (4) design PK/SK template per entity, (5) absorb remaining patterns into overloaded GSIs / Streams.
**Source**: Ch 7, 17.

### Single-table design
**When to use**: Default for production workloads with known access patterns and a need for low latency.
**How**: One table, multiple entity types, generic key names (`PK`/`SK`/`GSI1PK`/`GSI1SK`), key-prefix-encoded values (`ORG#`, `USER#`), `Type` attribute on every item. Co-locate related entities in shared item collections via shared partition key.
**Tradeoffs**: Steep learning curve, rigid to new patterns, painful for analytics. Skip for greenfield with unknown patterns or GraphQL backends (resolver model neutralizes the benefit).
**Source**: Ch 7, 8.

### Key overloading
**When to use**: Any table with more than one entity type. Foundation of single-table design.
**How**: Generic key names (`PK`/`SK`); prefix values by entity type (`ORG#name`, `USER#name`); ensure prefixes prevent cross-entity collisions; reuse the trick on GSIs.
**Source**: Ch 3, 7.

### Boundary implementation pattern
**When to use**: Every DynamoDB-backed app.
**How**: All DynamoDB-specific code (keys, type tags, expressions, name expansion) lives in one `data` module at the app's edge. Business logic only sees domain objects (`User`, `Order`).
**Source**: Ch 9.

### Attribute separation
**When to use**: Always. Never delete an application attribute because it's encoded in `PK`.
**How**: Distinguish indexing attributes (`PK`, `SK`, `GSI<N>PK/SK`) from application attributes (`Username`). Application attrs may construct indexing attrs, never the reverse. Allocate fresh `GSI<N>PK/SK` per GSI — never reuse a single attribute across indexes.
**Source**: Ch 9.

## One-to-Many Relationships (Ch 11)

### Denormalize with a complex attribute
**When to use**: Children are bounded (fits 400KB) AND never queried independently.
**How**: Store children as a list/map on the parent.
**Tradeoffs**: 400KB ceiling; can't query children alone.
**Example**: `Addresses` map on Customer (Ch 19); `CodeOfConduct` map on Repo (Ch 22).

### Denormalize by duplicating data
**When to use**: Duplicated data is immutable/near-immutable AND copy count is small.
**How**: Copy parent attributes onto each child item (violates 2NF).
**Tradeoffs**: Update storm if data ever changes across many copies.

### Composite primary key + Query API (DEFAULT)
**When to use**: Most one-to-many. Multiple access patterns on parent + children.
**How**: Parent and children share PK; differently-prefixed SK (`METADATA#`, `USER#`). One Query returns the collection; `begins_with(SK,"USER#")` slices by entity type.
**Source**: Ch 11.

### Secondary index + Query API
**When to use**: Base PK already taken (uniqueness, sharding, deeper hierarchy).
**How**: Same shape as above but on a GSI (`GSI1PK`/`GSI1SK`).
**Tip**: Position parent at one end and use `ScanIndexForward=False` for "parent + most recent N".

### Composite sort keys with hierarchical data
**When to use**: >2 hierarchy levels AND querying a level legitimately wants all sub-items beneath it (geo, org-chart, taxonomy).
**How**: Pack levels into SK with delimiter (`STATE#CITY#ZIP`); `begins_with` at the granularity you need.
**Avoid**: When you don't want sub-items at the queried level.

## Many-to-Many Relationships (Ch 12)

### Shallow duplication
**When to use**: Bounded count, only immutable subset needed in the parent context.
**How**: Embed a small list/map of child IDs+names on the parent. Solves ONE side only — pair with a Ch 11 strategy for the reverse.
**Example**: User stores embedded `Organizations` map (Ch 21).

### Adjacency list (HIGH-LEVERAGE)
**When to use**: Symmetric M:N where you query both sides and the relationship data is immutable.
**How**: Top-level items A and B each keyed on themselves. Relationship item lives in A's collection on base table (`PK=A, SK=B`); an inverted GSI (`GSI1PK=SK, GSI1SK=PK`) places the same relationship item in B's collection. Mutable attributes live ONLY on top-level items — one write to update.
**Source**: Ch 12. Reused in Ch 21 (Star), Ch 22 (AppInstallation).

### Materialized graph
**When to use**: Highly-connected data with many edge types (knowledge / social graphs).
**How**: Each node is its own item collection; GSIs reshuffle items into edge-based groupings.
**Tradeoffs**: Niche — DeBrie explicitly lacks a worked example.

### Normalization + multiple requests
**When to use**: Related data is highly mutable AND heavily duplicated (Twitter display names).
**How**: Sparse link items; read flow = Query for links + BatchGetItem to hydrate.
**Tradeoffs**: Two requests per read. Accept when write amplification of duplication would be worse.

## Filtering (Ch 13)

### Filter via partition key
**When to use**: Always — mandatory baseline for every non-Scan operation.
**How**: Make sure items you fetch together share a PK.

### Filter via sort key (simple + assembled)
**When to use**: Composite key, want to slice an item collection.
**How**: Simple — meaningful SK (date, score) with `BETWEEN`/`<`/`>=`/`begins_with`. Assembled — engineer SK ordering so `<=` / `>=` carves the collection (`ISSUE#... < REPO#... < STAR#...`).

### Composite sort key (concatenated values)
**When to use**: Always filter on 2+ attributes together AND one is enum-like.
**How**: Build SK as `ENUM#<otherValue>` (e.g., `CANCELLED#2019-07-15T...`) on a GSI. Query with exact enum + range on the second.
**Critical**: Enum field MUST come FIRST; reversing intersperses by high-cardinality field and breaks the filter.

### Sparse indexes (HIGH-LEVERAGE — DeBrie's favorite)
**When to use**: (a) Global filter on a rare subset of one entity type, OR (b) project a single entity type for enumeration.
**How**: GSI only projects items that carry its key attributes. Variant (a): add the GSI key attribute ONLY to items matching a predicate (only Admins get `GSI1SK=Admin`). Variant (b): give one entity type a unique attribute so only that type projects. REMOVE the attribute later to evict from the index.
**Tradeoffs**: Variant (b) doesn't combine with index overloading. Weaponizes attribute absence — easy to break by accidentally setting the attribute.
**Source**: Ch 13, with extended use in Ch 18 (Session UserIndex), Ch 20 (UserIndex + unread Messages GSI1).

### Filter Expressions (ANTI-PATTERN if misused)
**When to use**: ONLY (1) trim response payload, (2) simplify client logic, (3) TTL deletion safety net.
**How**: Applied AFTER the 1MB read — you PAY for everything filtered out. Acceptable only at 30-40%+ hit rate OR when pre-filter result is well under 1MB.
**Trap**: Breaks `Limit=N` semantics (no guarantee you'll get N items back).
**Lesson**: Ch 21 accepted one for Open/Closed Issues; Ch 22 walked it back after production over-fetching exposed the failure mode.

### Client-side filtering
**When to use**: Small dataset (sub-1MB), many flexible filter/sort columns, awkward-to-model patterns ("find gaps in a calendar").
**How**: Fetch a small slice, filter in browser/client.
**Quote**: Rick Houlihan — "The browser is sitting in a 99% idle loop. Give it something to do!"

## Sorting (Ch 14)

### Sorting basics
**When to use**: Every model.
**How**: Sort keys are scalars sorted by UTF-8 bytes; uppercase precedes lowercase — normalize case. For timestamps, use sortable formats (epoch number, ISO-8601), never `"May 26, 1988"`. For sortable unique IDs prefer **KSUIDs** (27-char, time-prefixed) over UUIDv4.

### Sort on changing attributes via GSI
**When to use**: Leaderboards, "most recently updated" feeds, any mutable sort dimension.
**How**: Keep base PK on immutable attributes; project the mutable sort attribute (`UpdatedAt`, score) as a GSI sort key. DynamoDB handles re-sort on replication — avoids delete+create on the base key.

### ScanIndexForward=False for "most recent N"
**When to use**: Any "latest N" / "top N" access pattern on a composite key.
**How**: Read the head of the B-tree backwards. Position parent so children sort on the side you'll read from; pair with `Limit=N`.

### Two relational access patterns in one item collection
**When to use**: Parent has two child entity types, each needing "fetch parent + children", and you want to avoid an extra GSI.
**How**: Sort one child type BEFORE the parent (`#TEAM#...`), the other AFTER (`USER#...`). Query `SK <= parent` + reverse scan for one direction; `SK >= parent` forward for the other.
**Example**: Repo in the middle of Issues (before) and Stars (after) in Ch 21.

### Zero-padding numeric sort keys
**When to use**: Any string SK embedding an integer.
**How**: Fixed-width pad (`f"#{n:07d}"`); pick width well beyond realistic max; alert at ~30% capacity. Without padding, `"10"` sorts before `"2"`.

### Faking ascending order (inverted-number trick)
**When to use**: Two child types in one collection, both numeric, both want descending fetch from the parent in the SAME scan direction. Or to combine status filter + reverse sort in a single SK (Ch 22).
**How**: Store `MAX - n` zero-padded instead of `n`. Issue #15 with MAX=99999999 becomes `99999984`. Forward scan now yields descending-by-id.
**Tradeoffs**: DeBrie explicitly names it "weird," "machine code," "indecipherable outside context" — document it, and offer the alternative (extra GSI) if readability matters more than index count.
**Source**: Ch 14, applied in Ch 22 Open/Closed Issues refactor.

## Migrations (Ch 15, 22)

### Add new attribute to existing entity (LAZY)
**When to use**: New non-indexed attribute.
**How**: Just start writing it; handle absence with `.get('Attr', default)` at the data boundary. No ETL.

### Add new entity type, no relations
**How**: Define new PK/SK pattern; start writing into a new item collection. No ETL.

### Add new entity into existing (empty) item collection
**When to use**: New "fetch parent + children" pattern AND the parent's existing collection is empty/unused.
**How**: Design child SK to colocate with parent's PK. No ETL.
**Example**: Gists into the User collection (Ch 22).

### Add new entity into a new item collection (BACKFILL)
**When to use**: New "fetch parent + children" pattern AND parent's collection is in use.
**How**: Add `GSI1PK`/`GSI1SK` on new children at write time; Scan + UpdateItem to backfill the same attrs onto existing parents.

### Join existing items into a new item collection
**When to use**: New filter/sort/grouping needed across existing items — no new entity type.
**How**: Same Scan + UpdateItem ETL as above to add new GSI attributes.

### Parallel scans for backfill
**When to use**: Any non-trivial migration backfill.
**How**: Pass `TotalSegments=N` and `Segment=0..N-1` to each worker; DynamoDB coordinates. Scan reads the whole table 1x in parallel — provision capacity or use on-demand to absorb burst.

### Migration difficulty ladder
**Order (easiest → hardest)**: (1) new attribute, (2) new entity no rel, (3) new entity rel into existing collection, (4) new entity rel into new collection, (5) refactoring existing patterns. Tackle in order; most migrations live at the easy end.

## Additional Patterns (Ch 16)

### Multi-attribute uniqueness via tracking items
**When to use**: Independent uniqueness on 2+ attributes (e.g., username AND email).
**How**: `TransactWriteItems` with one Put per attribute, each guarded by `attribute_not_exists(PK)`. The "email" item exists solely as a uniqueness marker.
**Tradeoff**: If you ALSO query by email, you must duplicate user data onto the email item — and every update becomes a transaction.

### Sequential IDs (atomic counter + Put)
**When to use**: Human-facing sequential numbers (issue numbers, order numbers).
**How**: `UpdateItem` with `SET count = count + 1` and `ReturnValues=UPDATED_NEW` on the parent; use returned value as new child ID; then `PutItem` the child. Two round trips — accept the cost.

### Pagination with LastEvaluatedKey
**How**: Pass returned `LastEvaluatedKey` as `ExclusiveStartKey` next call. Encode as URL cursor (`?before=<orderId>`). No OFFSET exists.
**Trap**: FilterExpression breaks `Limit=N` semantics.

### Singleton items
**When to use**: Global state with exactly one instance (feature flags, curated front-page, "max N jobs").
**How**: Fixed PK/SK (`PK=JOBS, SK=JOBS`). Mutate transactionally with related items. Trivially cacheable; copyable across read shards.
**Example**: `BRANDS`, `FRONTPAGE`, `EDITORSCHOICE` (Ch 20).

### Reference counts (transaction)
**When to use**: UI shows "N likes/stars/comments" without listing them.
**How**: `TransactWriteItems` with (1) Put child guarded by `attribute_not_exists(PK)` (idempotency), (2) UpdateItem on parent incrementing count guarded by `attribute_exists(PK)`. Counter never drifts from underlying items.

## API Usage Patterns (Ch 5, 6)

### No-ODM policy
**How**: Skip full ODMs. Acceptable thin helpers: AWS Document Client (Node), Boto3 `Table` resource (Python), DynamoDB Toolbox-style entity helpers that don't hide the API.

### BETWEEN as default sort-key operator
**How**: BETWEEN subsumes `<`, `<=`, `>`, `>=`, `=`. Default to it.

### ExpressionAttributeNames everywhere
**How**: 573 reserved words — use `#name` aliases uniformly even when the name looks safe.

### ReturnValues to fold a read into a write
**How**: `ReturnValues=ALL_NEW` / `UPDATED_NEW` on UpdateItem returns post-write state — saves a round trip for counters and conditional updates.

### ReturnItemCollectionMetrics as LSI tripwire
**How**: Returns approximate collection size; alert at ~7-8 GB so you have runway before writes fail at 10 GB.

### ConditionExpression for uniqueness
**How**: `attribute_not_exists(PK)` on PutItem eliminates read-then-write races. DeBrie: >90% of his PutItems use this.

## Advanced Real-World Patterns (Ch 18-22)

### TTL + FilterExpression belt-and-suspenders (Ch 18)
**When to use**: Any time-bounded entity (sessions, OTPs, signed URLs).
**How**: Set DynamoDB TTL attribute as epoch seconds. Layer a `FilterExpression #ttl > :now` on reads because AWS only guarantees TTL deletion within 48 hours.
**Bonus**: Store duplicate timestamps — epoch for TTL, ISO-8601 for humans.

### KEYS_ONLY GSI for "lookup then act"
**When to use**: GSI exists only to find primary keys for follow-up writes (e.g., revoke all sessions for a user).
**How**: Project KEYS_ONLY; minimize GSI storage and write cost.
**Example**: Session `UserIndex` (Ch 18).

### Time-bucket sharding for "latest N" (Ch 20)
**When to use**: High-volume globally-ordered "latest" patterns that would naively create hot/fat partitions.
**How**: Partition key = `DEALS#<TruncatedTimestamp>` (e.g., truncate to day). App walks backward bucket-by-bucket with a max-attempts guard if a bucket is short. Bounds partition size AND distributes traffic.

### In-DynamoDB read-shard cache (Ch 20)
**When to use**: Hot read on a small payload (front page) that would throttle a single partition.
**How**: Duplicate the payload across N `DEALSCACHE#<N>` items in different partitions. Reads pick a random shard. Refresh via DynamoDB Streams + Lambda when the underlying data changes. No Redis required.

### Watchers/subscribers share a partition keyed by what's watched (Ch 20)
**How**: `BRANDWATCH#<Brand>` PK with `USER#<Username>` SK. One Query enumerates all watchers for notification fanout. Pair with Streams + Lambda for reactive out-of-band fanout.

### Sparse index for status filtering within an entity (Ch 20)
**When to use**: "Show only items in state X" where state changes over time (unread messages, open tickets).
**How**: Items get the GSI keys ONLY in state X; transitioning out of X uses `REMOVE` on the indexed attributes to evict from the index. Read path swaps between base table and GSI with no FilterExpression.

### Parent-in-the-middle item collection (Ch 21)
**When to use**: One entity has two related collections, each fetched with the parent, going opposite directions.
**How**: Parent SK sorts between the two child types. Query `SK<=parent` descending for one; `SK>=parent` ascending for the other. Saves a GSI.
**Example**: Repo between Issues (before) and Stars (after) in Ch 21.

### Cross-entity namespace uniqueness via shared key shape (Ch 21)
**When to use**: Two entity types must not collide on a shared name (GitHub Users and Orgs).
**How**: Both entities use the same PK shape (`ACCOUNT#<name>`). DynamoDB's primary-key uniqueness enforces the cross-entity constraint for free.

### Adjacency list join item in TWO empty collections (Ch 22)
**When to use**: M:N where both sides have empty existing item collections.
**How**: Join item lives in side A's base-table collection AND side B's GSI1 collection — zero changes to existing items beyond the join writes themselves.
**Example**: `AppInstallation` between App (base) and Repo (GSI1).

### Reuse empty item collections during migration (Ch 22)
**When to use**: Adding a new child entity to an existing parent.
**How**: Check whether the parent's existing item collection contains only the parent. If yes, drop the new child in — no ETL required.

### Scan + UpdateItem ETL script (Ch 22)
**How**: Scan filtered by `Type`, loop with `LastEvaluatedKey`, UpdateItem each result adding the new GSI keys. Production version layers parallel scans, BatchWriteItem, retries, checkpointing. The standard migration tool — keep it in your toolbox.
