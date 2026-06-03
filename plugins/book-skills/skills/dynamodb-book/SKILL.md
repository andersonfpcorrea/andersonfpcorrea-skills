---
name: dynamodb-book
description: "Knowledge base from \"The DynamoDB Book\" by Alex DeBrie (v1.0, 2020). Use when designing DynamoDB tables, applying single-table design, choosing modeling strategies (one-to-many, many-to-many, filtering, sorting, migrations), reviewing access patterns, or referencing DDB API/expression usage."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, framework name, chapter number, or 'index']"
---

# The DynamoDB Book
**Author**: Alex DeBrie  |  **Pages**: 448  |  **Chapters**: 22  |  **Generated**: 2026-05-23

## How to Use This Skill

- **Without arguments** — load the core frameworks and mental models below
- **With a topic** — ask about `sparse index`, `single-table design`, `adjacency list`, etc.; I find the relevant chapter
- **With chapter** — ask for `ch11` or `ch21`; I load that chapter file
- **Browse** — ask "what chapters do you have?" or pass `index`

For details beyond the core frameworks below, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md` files in this skill.

---

## Core Frameworks & Mental Models

### 1. Access patterns first (Ch 7) — the methodology

DynamoDB modeling is **access-pattern-first, not entity-first**. You cannot model entities generically and add flexible queries later — every access pattern must be enumerated **before** the primary key is designed. Modeling DynamoDB like a relational database guarantees a worse outcome than just using a relational database.

The 5-step process:
1. Build the ERD (entities + relationships)
2. List every access pattern up-front (UI-driven and API-driven)
3. Model the primary key for the most common pattern
4. Add GSIs to serve additional patterns
5. Validate every access pattern lands on PK or GSI without Scan

If you cannot list your access patterns, you are not ready to design a table. Use a relational DB until you can.

### 2. Single-table design (Ch 8) — the technique

Store multiple entity types in **one DynamoDB table** with overloaded primary keys, so heterogeneous items live together in an **item collection** (items sharing a partition key) and can be fetched in a single `Query`.

- **Benefit**: one round-trip instead of N serial requests; lower latency and lower cost.
- **Downsides**: steep learning curve, harder to add new access patterns later, BI/analytics harder.
- **When NOT to use**:
  - **Greenfield serverless apps** with rapidly-evolving access patterns — agility outweighs perf.
  - **GraphQL apps** where resolvers fire per-field anyway (DataLoader makes single-table benefits moot).

### 3. Key overloading (Ch 3) — the enabling primitive

Use **generic attribute names** for keys (`PK`, `SK`, `GSI1PK`, `GSI1SK`, …) and put **prefixed entity-type values** inside (`ORG#acme`, `USER#alex`, `REPO#repo1#ISSUE#42`). One physical column holds different logical keys per entity type. This is what makes single-table design possible and powers most strategies in chapters 11-16.

### 4. The five expression types (Ch 6) — daily API vocabulary

| Expression | Used with | Constrains | Evaluated |
|---|---|---|---|
| **KeyConditionExpression** | Query | PK exact match + SK conditions | At index lookup — cheap |
| **FilterExpression** | Query, Scan | Any attribute | **AFTER fetch** — does NOT save RCU |
| **ProjectionExpression** | All reads | Which attributes returned | After fetch (saves bandwidth only) |
| **ConditionExpression** | Put/Update/Delete | Item must match before write | Before write — fails with `ConditionalCheckFailed` |
| **UpdateExpression** | UpdateItem | What to mutate | At write |

**The biggest API anti-pattern** in the book: using `FilterExpression` to reduce reads. It runs after the 1MB query response is assembled and silently breaks `Limit=N`. If you need to filter by attribute X, design X into a key.

### 5. Relationship strategy catalog (Ch 11-12)

DDB has no joins. Pick the right strategy per relationship per access pattern.

**One-to-many (Ch 11) — five strategies, in rough order of preference:**
1. **Denormalize with a complex attribute** (list/map on parent) — bounded children, queried only with parent.
2. **Denormalize by duplicating** — children are immutable and small.
3. **Composite PK + Query** — children always fetched with parent, can grow unbounded.
4. **Secondary index + Query** — also need to look up children by their own ID.
5. **Composite sort key (hierarchical)** — deep hierarchies queried with prefix ranges.

**Many-to-many (Ch 12) — four strategies:**
1. **Shallow duplication** — one side has the few-and-immutable attributes of the other.
2. **Adjacency list** — store both sides as separate items under one PK; inverted GSI flips the direction. (Most common — the canonical pattern.)
3. **Materialized graph** — full graph in DDB; niche, used at very high scale.
4. **Normalization + multiple requests** — accept N requests when the join is rare.

### 6. Filtering — almost always done in the key (Ch 13)

"Filtering in DynamoDB is almost exclusively focused on your primary key." Six strategies, five of them ways to put the filter into the PK or SK of base table or GSI. The most important:

- **Sparse index** — only items with a given attribute land in the GSI. Powerhouse pattern: "active sessions only," "open issues only," "deals expiring today." Used everywhere in Ch 18-22.
- **Composite sort key** — `STATUS#DATE` enables both "orders by status" and "orders by status sorted by date" with a single GSI.
- **Begins_with / BETWEEN** on the sort key for range queries.

Sixth strategy is **FilterExpression** — see anti-pattern above.

### 7. Sorting is a key-design problem, not a query-time problem (Ch 14)

There is no `ORDER BY`. Items in a partition are stored as a B-tree ordered **lexicographically (UTF-8 bytes)** on the sort key. You must arrange your items so they are **already sorted** in the order you need.

- **Zero-pad numeric sort keys**: without it, `"10"` sorts before `"2"`. Use `f"{n:010d}"`.
- **Use ISO-8601 timestamps** (UTC, lexicographic = chronological).
- **Reverse a Query** with `ScanIndexForward=false` — don't fight it with key encoding.
- **Inverted-number trick** (Ch 22): `MAX − value` to fake ascending order on a naturally descending dimension. DeBrie admits it's "machine code" — readable alternative is to add a separate GSI.

### 8. Migrations are tractable (Ch 15, 22)

The biggest objection to single-table design has a clear answer: classify the change, then apply the right strategy.

- **Additive changes** (new attribute, new entity type, new item collection) — **free**. Write new format going forward; old items don't need touching. Application attributes are schemaless and always free to add.
- **Mutating changes** (rewriting existing items, joining items into a new collection) — require a **Scan + UpdateItem ETL job**. Use parallel scans for speed; cost is one full table read.

Only **indexing attributes** count when judging "additive." Application attributes can always be added without touching existing rows.

### 9. The hard limits — the four numbers to memorize (Ch 3)

| Limit | Value | Consequence |
|---|---|---|
| Item size | **400 KB** | Caps "denormalize as a list/map" strategies |
| Query/Scan response | **1 MB** | Forces pagination; FilterExpression breaks Limit semantics |
| Per-partition throughput | **3,000 RCU / 1,000 WCU** | Hot-partition risk for "fetch latest" patterns → time-bucket sharding |
| LSI item-collection size | **10 GB** | Hard cap on one collection if any LSI exists |

(Plus: 20 GSIs/table, 5 LSIs/table, 48h TTL deletion SLA, 25 items per TransactWriteItems / BatchWriteItem, 100 per BatchGetItem.)

### 10. DeBrie's strongest opinions (anti-patterns from across the book)

- **Don't use an ORM/ODM** (Ch 5). Hides the table design, costs you control over keys. Two narrow exceptions: thin marshaling helpers and per-entity classes you wrote.
- **Don't use FilterExpression to reduce reads** (Ch 6, 13). Already discussed.
- **Don't reuse attributes across multiple indexes** (Ch 9). Coupling — one schema change breaks multiple queries.
- **Don't model relationally first, then "move to DynamoDB"** (Ch 7, 8). Start from access patterns.
- **Don't Scan in production** (Ch 4) — except for ETL jobs and admin tooling.
- **Don't forget the `Type` attribute** on every item (Ch 9) — essential for filtering by entity in single-table designs and for downstream ETL.

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-what-is-dynamodb.md) | What is DynamoDB? | Five misconceptions, when-to-use checklist |
| [ch02](chapters/ch02-core-concepts.md) | Core Concepts in DynamoDB | Table/item/attribute, PK + SK, item collection |
| [ch03](chapters/ch03-advanced-concepts.md) | Advanced Concepts | Streams, TTL, partitions, consistency, **limits**, key overloading |
| [ch04](chapters/ch04-three-api-action-types.md) | The Three API Action Types | Item / Query / Scan buckets, time-complexity model |
| [ch05](chapters/ch05-using-the-dynamodb-api.md) | Using the DynamoDB API | ExpressionAttributeNames/Values, no-ORM rule, optional request properties |
| [ch06](chapters/ch06-expressions.md) | Expressions | The 5 expression types; FilterExpression anti-pattern |
| [ch07](chapters/ch07-approach-data-modeling.md) | Approach to Data Modeling | Access-patterns-first, 5-step process, entity + access-pattern chart |
| [ch08](chapters/ch08-single-table-design.md) | Single-Table Design | What/why/downsides, two opt-out cases |
| [ch09](chapters/ch09-modeling-to-implementation.md) | From Modeling to Implementation | Boundary pattern, Type attribute, shorten attribute names |
| [ch10](chapters/ch10-importance-of-strategies.md) | The Importance of Strategies | Why "strategy" is the vocabulary |
| [ch11](chapters/ch11-one-to-many-strategies.md) | One-to-many strategies | All 5 strategies + decision table |
| [ch12](chapters/ch12-many-to-many-strategies.md) | Many-to-many strategies | All 4 strategies, especially adjacency list |
| [ch13](chapters/ch13-filtering-strategies.md) | Filtering strategies | All 6, especially sparse indexes |
| [ch14](chapters/ch14-sorting-strategies.md) | Sorting strategies | Zero-padding, inverted number, two access patterns in one collection |
| [ch15](chapters/ch15-migration-strategies.md) | Migration strategies | Additive vs mutating, parallel scans, ETL pattern |
| [ch16](chapters/ch16-additional-strategies.md) | Additional strategies | Uniqueness, atomic counters, pagination, singletons, reference counts |
| [ch17](chapters/ch17-data-modeling-examples-intro.md) | Examples intro | How to read the worked examples |
| [ch18](chapters/ch18-session-store.md) | Session Store | TTL, sparse index, simple PK example (4 patterns) |
| [ch19](chapters/ch19-ecommerce-app.md) | E-commerce app | Composite keys, multi-attribute uniqueness (6 patterns) |
| [ch20](chapters/ch20-big-time-deals.md) | Big Time Deals | Time-bucket sharding, read-shard cache, singletons (23 patterns) |
| [ch21](chapters/ch21-recreating-github-backend.md) | Recreating GitHub's Backend | Full masterclass — 3 GSIs, 24 access patterns |
| [ch22](chapters/ch22-handling-migrations-github.md) | Migrations on the GitHub model | Migration difficulty ladder applied; inverted-number SK |

---

## Topic Index

- **Access patterns** → ch07, ch11-16, ch18-22
- **Adjacency list** → ch12, ch21
- **Atomic counter / sequential IDs** → ch16, ch21
- **BatchGetItem / BatchWriteItem** → ch04, ch05
- **Capacity (on-demand vs provisioned)** → ch01, ch02
- **Composite primary key** → ch02, ch08, ch11, ch14
- **Composite sort key** → ch11, ch13, ch14
- **ConditionExpression** → ch06, ch16
- **Consistency (eventual vs strong)** → ch03
- **Denormalization** → ch11
- **DynamoDB Streams** → ch03, ch20
- **ERD** → ch07, ch18-22
- **ExpressionAttributeNames/Values** → ch05, ch06
- **FilterExpression (and anti-pattern)** → ch06, ch13
- **GSI / Global Secondary Index** → ch02, ch03, ch11-13, ch20-22
- **GSI overloading** → ch03, ch21
- **Hot partition / sharding** → ch03, ch20
- **Item collection** → ch02, ch08, ch11, ch14, ch21
- **KeyConditionExpression** → ch05, ch06
- **Key overloading** → ch03, ch08, ch19, ch21
- **Limits (item size, RCU, etc.)** → ch03
- **LSI / Local Secondary Index** → ch02, ch03
- **Migrations** → ch15, ch22
- **Modeling process (5 steps)** → ch07
- **Naming conventions (PK, SK, GSI1PK, …)** → ch09, ch21
- **Normalization (DDB version)** → ch07, ch12
- **No-ORM rule** → ch05
- **Pagination (LastEvaluatedKey)** → ch04, ch16
- **Parent-in-the-middle item collection** → ch21
- **Partition key** → ch02
- **Primary key (simple vs composite)** → ch02, ch08, ch18
- **ProjectionExpression** → ch06
- **Query (vs Scan vs item-based)** → ch04
- **Read-shard cache (in DDB)** → ch20
- **Reference counts** → ch16
- **RCU/WCU math** → ch01, ch03, ch04
- **Scan (and when not to)** → ch04, ch15
- **Single-table design** → ch08, ch21
- **Singleton items** → ch16, ch20
- **Sort key** → ch02, ch14
- **Sparse index** → ch13, ch18, ch20
- **Strategy (as concept)** → ch10
- **TTL** → ch03, ch18
- **Time-bucket sharding** → ch20
- **TransactGetItems / TransactWriteItems** → ch04, ch16, ch19
- **Type attribute** → ch09
- **UpdateExpression** → ch06, ch16
- **Worked examples (which one for which scale)** → ch17, ch18 (small), ch19 (medium), ch20-21 (large)
- **Zero-padding sort keys** → ch14

## Supporting Files

- [glossary.md](glossary.md) — ~125 terms alphabetical, one-line each
- [patterns.md](patterns.md) — every named strategy with When/How/Tradeoffs
- [cheatsheet.md](cheatsheet.md) — single-page reference (limits, picker tables, anti-patterns, naming, pricing)

---

## Scope & Limits

This skill covers the book content only (v1.0, April 2020). It does not include:
- DynamoDB features released after April 2020 (e.g., PartiQL improvements, import from S3, NoSQL Workbench updates, on-demand cost reductions)
- Current AWS pricing (see AWS docs)
- Implementation details of any specific SDK version

For hands-on work, combine with the current AWS DynamoDB documentation. The book's modeling principles (access-patterns-first, single-table design, the strategy catalog) remain authoritative.
