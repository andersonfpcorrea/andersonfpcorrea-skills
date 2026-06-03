# Chapter 7: How to Approach Data Modeling in DynamoDB

## Core Idea
DynamoDB modeling is access-pattern-first, not entity-first. You cannot model generically and add flexible queries later — every access pattern must be enumerated before the primary key is designed. Modeling DynamoDB like a relational database guarantees a worse outcome than just using a relational database.

## Frameworks Introduced
- **DeBrie's 5-Step DynamoDB Modeling Process**:
  1. **Understand your application** — domain, scale, latency requirements.
  2. **Create an Entity-Relationship Diagram (ERD)** — entities, attributes, relationships (1:1, 1:N, N:M).
  3. **Write out ALL access patterns** — every read and write, with parameters, recorded in a chart.
  4. **Model your primary key structure** — entity chart with PK/SK templates per entity type; satisfy uniqueness first, then "fetch many" patterns.
  5. **Handle remaining access patterns with secondary indexes and streams** — overload GSIs; avoid one-index-per-pattern.

- **Two strategies for gathering access patterns**:
  - **API-centric**: List each REST endpoint + expected response shape. Use when building a REST API.
  - **UI-centric**: Walk each screen/URL and list the data needed to render it. Use for SSR or backends-for-frontends.

- **Access Pattern Chart**: Columns = Entity, Access Pattern, Index, Parameters, Notes. Left side filled before modeling; right side filled as you model.

- **Entity Chart**: Columns = Entity, PK template, SK template. Copy each ERD entity in, then iterate as you model.

## Key Concepts
- **ERD (Entity-Relationship Diagram)**: Boxes = entities with attributes; diamonds/lines = relationships and cardinality.
- **Primary key prefixing**: `CUSTOMER#<CustomerId>` / `ORDER#<OrderId>` to distinguish entity types sharing PK/SK attributes.
- **Generic key attribute names**: Use `PK`, `SK`, `GSI1PK`, `GSI1SK` — descriptive names break down when multiple entity types share keys.
- **Overloaded index**: A single GSI that serves multiple access patterns by storing differently-shaped values per entity type.
- **Composite primary key**: PK + SK; needed any time an access pattern fetches multiple items or multiple entity types together.
- **Denormalization**: Storing duplicated data deliberately because storage is cheap and joins are unavailable.
- **Normalization forms (1NF/2NF/3NF)**: 1NF — atomic columns; 2NF — non-key depends on whole key; 3NF — no transitive dependencies. Relational concepts that you intentionally violate in DynamoDB.

## Mental Models
- **Use access-pattern-first design when** modeling DynamoDB — never ship an ERD straight to the table.
- **Use composite keys when** any access pattern returns multiple items or heterogeneous entity types.
- **Use a single table with multiple entity types when** you'd otherwise need joins.
- **Use prefixes (`ENTITY#<id>`) when** different entity types could collide on key values.
- **Think of the key as known-at-read-time data**: If the client won't have a value when querying, that value can't go in the key.

## Anti-patterns
- **Modeling DynamoDB like a relational DB**: You lose NoSQL benefits AND get worse ergonomics than just using SQL.
- **Faking joins in application code**: Serial GetItem → Query is exactly what single-table design exists to avoid.
- **Designing for "future flexibility"**: DynamoDB doesn't reward this; it punishes it. Model only what you need.
- **Adding `CreatedAt` (or any unknown-at-read-time value) to a primary key**: Ensures uniqueness but breaks retrieval — the client doesn't know the timestamp.
- **One secondary index per access pattern**: Wasteful. Overload GSIs by reusing `GSI1PK`/`GSI1SK` across entity types.
- **Skipping the ERD or access-pattern chart**: The most common cause of "DynamoDB is bad" complaints; the tool was used for the wrong job (raking leaves with a screwdriver).
- **Descriptive PK names like `CustomerId`** in a single-table design — they lie when other entities share the column.

## Code Examples
```
Entity            PK                          SK
Customer          CUSTOMER#<CustomerId>       METADATA#<CustomerId>
CustomerOrder     ORDER#<OrderId>             METADATA#<OrderId>
```
- **What it demonstrates**: Entity chart with prefixed key templates per entity type.

```
Entity     Access Pattern        Index  Parameters       Notes
Session    Create Session
Session    Get Session
Session    Delete (time-based)
Session    Delete (manual)
```
- **What it demonstrates**: Access pattern chart — left side first, right side filled during modeling.

## Reference Tables

| Relational DB | DynamoDB |
|---|---|
| Joins reassemble data at read time | Pre-assemble data into item collections |
| Normalize (storage scarce) | Denormalize (storage cheap, compute scarce) |
| One table per entity type | Multiple entity types per table |
| WHERE clause filters any column | Filtering must be baked into primary key / GSI |
| Descriptive column names | Generic `PK`, `SK`, `GSI1PK`, `GSI1SK` |
| Flexible queries added later | All access patterns enumerated upfront |
| Vertical scaling (single machine) | Horizontal scaling (partitioned) |

| Normal Form | Plain English |
|---|---|
| 1NF | Each column value is atomic |
| 2NF | Non-key attributes depend on the whole key |
| 3NF | No transitive dependencies between non-key attributes |

## Key Takeaways
1. Data modeling is driven entirely by access patterns — no exceptions.
2. Write every access pattern down in a chart before designing the primary key.
3. Build an ERD even for small models — it forces thinking and serves as a permanent artifact.
4. Use composite keys whenever a pattern fetches multiple items or entity types.
5. Use prefixes on key values (`USER#<id>`) and generic attribute names (`PK`, `SK`) so multiple entity types coexist cleanly.
6. Denormalize aggressively; data integrity becomes an application concern, not a database concern.
7. Overload secondary indexes — one GSI can serve many access patterns.
8. Iteration is expected; even experienced modelers do several passes before reaching a lean design.

## Connects To
- **Ch 3**: Primary key fundamentals — the structure being designed here.
- **Ch 6**: Why filter expressions cannot fix missing access patterns.
- **Ch 8**: Single-table design — the natural output of "multiple entity types per table."
- **Ch 9**: Implementation patterns once the model is set.
- **Ch 11**: One-to-many strategies that determine entity chart shape.
- **Ch 15**: Migration strategies for when access patterns change post-launch.
- **Ch 19, 21**: End-to-end e-commerce and GitHub examples applying this process.
