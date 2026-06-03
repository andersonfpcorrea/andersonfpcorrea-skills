# Chapter 8: The What, Why, and When of Single-Table Design in DynamoDB

## Core Idea
Single-table design stores multiple entity types in one DynamoDB table with overloaded primary keys so heterogeneous items can be fetched in a single Query (item collection). The primary benefit is performance — one round-trip instead of serial requests. The cost is steep learning, inflexibility to new access patterns, and harder analytics. There are two specific cases where the costs outweigh the benefits.

## Frameworks Introduced
- **Single-Table Design**: One DynamoDB table per application (or microservice) containing all entity types. Primary keys are designed so related items share a partition key and thus live in the same item collection, accessible via a single Query.
  - When to use: Default for production DynamoDB workloads with known access patterns.
  - How: (1) Enumerate access patterns. (2) Place related entities in the same item collection by sharing a partition key. (3) Use overloaded generic key names (`PK`, `SK`, `GSI1PK`, `GSI1SK`). (4) Distinguish entity types via key prefixes and a `Type` attribute.

- **The Two Exceptions** (when single-table design is NOT worth it):
  1. **New/greenfield applications prioritizing developer agility** — access patterns are unknown; serverless apps that won't immediately hit DynamoDB-scale loads can use a "Faux-SQL" multi-table approach for flexibility.
  2. **GraphQL applications** — resolvers are independent and execute serially; you make N database requests regardless of table design, eliminating single-table's main benefit.

## Key Concepts
- **Item collection**: All items in a table or index sharing the same partition key. Retrievable in one Query.
- **Pre-joining**: Designing partition keys so heterogeneous items that need to be read together already share an item collection.
- **Overloaded primary key**: Same key attributes (`PK`/`SK`) hold different value patterns for different entity types.
- **OLTP vs OLAP**: DynamoDB is built for OLTP (fast, single-record); analytics (OLAP) requires exporting to Redshift/Athena/S3.
- **Faux-SQL**: Using DynamoDB but normalizing across multiple tables in a relational style — gives up single-table's perf benefits for flexibility.
- **Forrest Brazeal's analogy**: "A well-optimized single-table DynamoDB layout looks more like machine code than a simple spreadsheet."

## Mental Models
- **Use single-table design when** you need consistent low-latency performance, you know your access patterns, and you'll scale.
- **Use multi-table (Faux-SQL) when** developer agility and flexibility matter more than ms-level latency (greenfield apps, prototypes, early-stage products).
- **Skip single-table design with GraphQL because** resolvers fire serial DB calls per type — you pay single-table's cost without getting its single-request benefit.
- **Think of single-table modeling as compiling**: you're hand-optimizing a layout for specific reads, the way a compiler emits machine code for specific operations.

## Anti-patterns
- **Multiple serial requests in application code** to simulate joins — defeats the entire point of NoSQL.
- **Using normalized, per-entity tables in DynamoDB** for an at-scale application — you get neither relational query flexibility nor DynamoDB performance.
- **Choosing multi-table because single-table feels weird** — learning difficulty isn't a valid technical reason.
- **Using single-table design when access patterns are still being discovered** — migrations are possible but painful early on.
- **Forcing single-table onto a GraphQL backend** — wasted effort; GraphQL's resolver model neutralizes the benefit.

## Code Examples
```
# Item collection example — Tom Hanks's movies share a partition key
PK (Actor)    SK (Movie)
Tom Hanks     Cast Away
Tom Hanks     Toy Story
```
- **What it demonstrates**: Items sharing PK form an item collection, retrievable via a single Query.

```graphql
query { User(id:112233) {
    firstName lastName addresses
    orders { orderDate amount status }
} }
```
- **What it demonstrates**: One GraphQL request — but internally the User resolver fires first, then the Order resolver runs serially, producing N DynamoDB calls.

## Reference Tables

| Benefit of Single-Table | Real Impact |
|---|---|
| Single round-trip for heterogeneous reads | Major — main motivator |
| Reduced operational overhead (fewer alarms/metrics) | Marginal |
| Capacity buffer sharing across entity types (provisioned) | Marginal; zero on On-Demand |

| Downside | Impact |
|---|---|
| Steep learning curve | Real, but not a valid excuse to opt out |
| Inflexibility to new access patterns | Real — requires ETL migrations |
| Difficulty exporting for analytics | Real — must "unwind" the table for Redshift/Athena |

| When NOT to use single-table | Reason |
|---|---|
| Greenfield serverless app, unclear patterns | Developer agility > sub-30ms latency |
| GraphQL backend | Resolver model already issues N serial DB calls; single-table benefit nullified |
| Simple CRUD with one entity | No item collections to build |
| Heavy ad-hoc analytics needs | OLAP unwind is painful |

## Key Takeaways
1. The point of single-table design is fewer round trips to DynamoDB — ideally one per access pattern.
2. Achieve it by placing items that are read together in the same item collection (shared partition key).
3. Operational/cost benefits are real but marginal; performance is the real motivator (and zero cost benefit on On-Demand).
4. Single-table is rigid — adding new access patterns post-launch may require an ETL migration.
5. Analytics requires re-normalizing your table; bake export pipelines in early.
6. Skip single-table for greenfield serverless apps where patterns are still being discovered.
7. Skip single-table for GraphQL — its resolver model fires serial DB calls regardless.
8. Even if you opt out, learn single-table design first so you know what you're giving up.

## Connects To
- **Ch 3, 7**: Primary key design and access-pattern-first methodology — single-table is the natural product.
- **Ch 6**: Key condition expressions enabling single-Query collection reads.
- **Ch 9**: Implementation patterns (Type attribute, indexing attributes) that make single-table livable.
- **Ch 11**: One-to-many strategies — the core mechanism that builds item collections.
- **Ch 15**: Migration strategies for when single-table designs need to evolve.
- **Ch 19, 21, 22**: Full-application single-table examples (e-commerce, GitHub, migrations).
