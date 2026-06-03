# DynamoDB Cheatsheet — DeBrie

## Hard Limits (Ch 3)

| Limit | Value |
|-------|-------|
| Max item size | 400 KB |
| Max Query/Scan response | 1 MB (before FilterExpression) |
| Max item collection size (with LSI) | 10 GB |
| Max RCU per partition | 3,000/sec |
| Max WCU per partition | 1,000/sec |
| Max GSIs per table | 20 |
| Max LSIs per table | 5 |
| Max attributes in primary key | 2 (PK + SK) |
| TTL deletion SLA | within ~48 hours |
| Reserved words | 573 (use `#name` aliases) |
| BatchGetItem max items | 100 |
| BatchWriteItem max items | 25 |
| TransactGetItems / TransactWriteItems max | 25 items |
| Storage replicas per partition | 1 primary + 2 secondaries |
| RCU = | 1 strong read OR 2 eventual reads / sec / 4 KB |
| WCU = | 1 write / sec / 1 KB |

## Capacity Modes

| Mode | Use when |
|------|----------|
| On-demand | Spiky/unknown traffic, dev/test, serverless, no planning |
| Provisioned | Stable predictable load, cost-sensitive, can capacity-plan |

## API Action Buckets (Ch 4)

| Bucket | Examples | Operates on |
|--------|----------|-------------|
| Item-based | Get/Put/Update/DeleteItem, Batch*, Transact* | Specific item(s), full PK required, base table only |
| Query | Query | Exactly one item collection (one PK) |
| Scan | Scan | Whole table/index — AVOID |

| Action | Cost / notes |
|--------|--------------|
| GetItem | 1 read |
| PutItem | 1 write (full item replace) |
| UpdateItem | 1 write (partial mutate) |
| BatchGetItem | 1 read per item, independent failures, ≤100 |
| BatchWriteItem | 1 write per item, Put/Delete only, ≤25 |
| TransactGetItems | 2× normal read cost, all-or-nothing, ≤25 |
| TransactWriteItems | 2× normal write cost, all-or-nothing, ≤25, cross-table OK |

## Five Expression Types (Ch 6)

| Expression | API | Constrains | When evaluated | Saves RCU? |
|------------|-----|------------|----------------|-----------|
| KeyCondition | Query | PK (=) + SK (range/begins_with) | Before read | YES |
| Filter | Query, Scan | Any attribute | AFTER read, before return | NO |
| Projection | All reads | Which attrs returned | After read | No (bandwidth only) |
| Condition | Put/Update/Delete + batch/transact | Any attr on item | Before write | N/A |
| Update | UpdateItem | Mutations | At write | N/A |

**Sort-key operators (KeyCondition):** `=`, `<`, `<=`, `>`, `>=`, `BETWEEN`, `begins_with()`. NOT: `contains()`, `ends_with()`.
**Update verbs:** `SET`, `REMOVE`, `ADD` (incr / set-insert), `DELETE` (set-remove).
**Condition functions:** `attribute_exists`, `attribute_not_exists`, `attribute_type`, `begins_with`, `contains`, `size`.

## Query Optional Params (Ch 5)

| Param | Use |
|-------|-----|
| `ConsistentRead=True` | Strong consistency (2× RCU). Not on GSI. |
| `ScanIndexForward=False` | Read SK descending — "most recent N" |
| `Limit=N` | Stop after N items (breaks with FilterExpression) |
| `ExclusiveStartKey` | Pagination cursor (use `LastEvaluatedKey` from prior response) |
| `ReturnValues` | `NONE/ALL_OLD/UPDATED_OLD/ALL_NEW/UPDATED_NEW` |
| `ReturnConsumedCapacity` | `NONE/TOTAL/INDEXES` |
| `ReturnItemCollectionMetrics` | Tripwire for LSI 10 GB limit |

## Index Types (Ch 2)

| | Key schema | Created | Consistency |
|---|------------|---------|-------------|
| LSI | Same PK as base, different SK | At table creation only | Strong (opt-in) or eventual |
| GSI | Any attributes | Anytime | Eventual only |

## One-to-Many Strategy Picker (Ch 11)

| Strategy | Use when | Avoid when |
|----------|----------|------------|
| Complex attribute (list/map) | Children bounded AND never queried independently | Unbounded count; queried on values |
| Duplicate data | Copied data immutable; few copies | Mutable data across many items |
| Composite PK + Query | DEFAULT; many patterns on parent+children | Base PK already needed elsewhere |
| Secondary index + Query | Base PK taken; deeper hierarchy | Base PK is free (use composite PK instead) |
| Composite sort key (hierarchical) | >2 levels AND want all sub-items beneath | Only want items at one level |

## Many-to-Many Strategy Picker (Ch 12)

| Strategy | Mutability | Read cost | Example |
|----------|-----------|-----------|---------|
| Shallow duplication | Immutable subset only | 1 GetItem | Class with student names |
| Adjacency list (inverted index) | Relationship immutable | 1 Query per side | Movies & Actors via Role |
| Materialized graph | Varied | 1 Query per edge type | Knowledge graph |
| Normalization + multiple requests | Highly mutable | Query + BatchGet | Twitter follows |

## Filtering Strategy Picker (Ch 13)

| Strategy | Cost | Use when |
|----------|------|----------|
| Partition key | O(1), free | Always — mandatory |
| Sort key (range / assembled) | Pay only matched range | Need to slice item collection |
| Composite sort key (enum first!) | Pay only matched range | Always filter on 2 attrs, one is enum |
| **Sparse index** | Pay only projected items | Rare-subset global filter OR find all of one entity type |
| Filter Expression | PAY for filtered-out items | Trim narrow result (30-40%+ hit, <1MB) |
| Client-side | Pay full fetch | Small dataset, flexible UI filtering |

## Sorting Rules (Ch 14)

- Sort key is the ONLY ordering mechanism — sorting is a write-time decision.
- Strings/binary = UTF-8 lex order (UPPER before lower). Numbers = numeric.
- Normalize case; pick sortable timestamps (epoch or ISO-8601); never display strings.
- Zero-pad embedded integers (`READING#00010`) — sized for max + headroom.
- KSUIDs > UUIDv4 when chronological sort matters (27 chars, time-prefixed).
- Mutable sort attrs go in a GSI, not base PK (PK attrs are immutable).
- `#` prefix sorts before letters → sort parent in middle for two child types in one collection.
- Fake ascending while reading forward: store `MAX - n` zero-padded.

## Migration Difficulty Ladder (Ch 15, 22)

| Difficulty | Situation | Strategy |
|------------|-----------|----------|
| Free | New non-indexed attribute | Lazy: write on next update |
| Free | New entity, no relations | Write to new item collection |
| Free | New entity into empty existing collection | Place children in parent's PK |
| Backfill | New entity needs new GSI on parent | Scan + UpdateItem ETL |
| Backfill | New access pattern on existing items | Scan + UpdateItem ETL |
| Any backfill | n/a | Use `TotalSegments`/`Segment` parallel Scan |

## Common Anti-Patterns (collected)

| Don't | Why |
|-------|-----|
| Use FilterExpression to select items | Runs AFTER read; pays for filtered-out; breaks `Limit=N` |
| Use Scan in production | Reads entire table; cost grows with data size |
| Use an ORM/ODM | Hides table design; pushes you toward relational shape |
| Reuse attributes across multiple indexes | Coupling — schema changes break multiple queries |
| Forget zero-padding on numeric sort keys | "10" sorts before "2" lexicographically |
| Model relationally then "move to DynamoDB" | Modeling starts from access patterns, not entities |
| Name PK keys after one entity (`UserId`) | Blocks key overloading / single-table design |
| Put mutable attrs (`UpdatedAt`, score) in base PK | PK is immutable; use a GSI |
| Composite SK with high-cardinality first | Defeats enum filter (`<date>#<status>` is wrong) |
| Rely on TTL for correctness | 48-hour SLA — verify with FilterExpression on read |
| Compound `username+email` into one PK and call both unique | Only the combination is unique — use two tracking items + transaction |
| Count children by querying them | Use reference counters on parent (transaction) |
| Pre-join unbounded children onto parent item | 400 KB ceiling |
| Use single-table for GraphQL backend | Resolvers fire serial DB calls anyway |
| Request `ConsistentRead=True` on a GSI | Not supported — errors |

## Modeling Workflow (Ch 7)

1. Understand the application (domain, scale, latency).
2. Build ERD (entities + relationships, no attributes).
3. Write down ALL access patterns up-front (chart: entity, pattern, index, params).
4. Design primary key for uniqueness + most common "fetch many" patterns.
5. Add overloaded GSIs for remaining patterns. Validate every pattern lands on PK/GSI without Scan.

## Naming Conventions (DeBrie style)

- Base table keys: `PK`, `SK` (generic, support overloading).
- GSI keys: `GSI1PK`, `GSI1SK`, `GSI2PK`, `GSI2SK`, ...
- Key values: prefixed by entity type — `ORG#acme`, `USER#alex`, `REPO#owner#name#ISSUE#0000042`.
- `Type` attribute on every item (`"User"`, `"Order"`) — invaluable for ETL/analytics.
- Separate indexing attributes from application attributes; never derive app attrs from keys.
- Implement all DDB-specific logic at a single data-access boundary module.

## Common Recipes (Ch 16)

| Need | Recipe |
|------|--------|
| Uniqueness on attr X | Tracking item with `PK=X#<val>` + `attribute_not_exists(PK)` |
| Multi-attribute uniqueness | TransactWriteItems: one tracking item per attribute |
| Atomic counter | `UpdateExpression="SET c = c + :inc"` |
| Sequential IDs | UpdateItem counter on parent (`RETURN UPDATED_NEW`) + PutItem child |
| Reference count | Transaction: Put child (idempotent) + Update parent counter |
| Pagination | Pass `LastEvaluatedKey` as `ExclusiveStartKey`; cursor in URL |
| Singleton (global state) | Fixed `PK=X, SK=X` item; mutate transactionally |
| Sparse index (drop item from index) | REMOVE the indexed attribute |
| Hot-key "latest N" | Time-bucket PK (`DEALS#<TruncatedTimestamp>`) |
| Hot-read fan-out | Duplicate payload across N shard items, read random one |

## Pricing (rough 2020, from book)

| Resource | Approx cost |
|----------|------------|
| 1M on-demand reads | $0.25 |
| 1M on-demand writes | $1.25 |
| 1 RCU/month provisioned | ~$0.09 |
| 1 WCU/month provisioned | ~$0.47 |
| Storage | $0.25/GB-month |
| GSI | Same RCU/WCU/storage costs as base table, per GSI |

*Verify current AWS pricing — values illustrative.*

## When NOT to use DynamoDB (Ch 1, 8)

- Unknown / evolving access patterns (early-stage product)
- GraphQL backend (resolvers issue serial DB calls regardless)
- Complex ad-hoc JOINs / OLAP analytics (export to Redshift/Athena/S3)
- Full-text search (use OpenSearch)
- Small team, low traffic, RDBMS already fine — DDB strengths don't apply
- Cloud portability is mandatory (consider Cassandra/MongoDB)
