# Chapter 3: Advanced Concepts

## Core Idea
Beyond the basics, mastering DynamoDB requires understanding Streams (CDC), TTL (auto-expiry), partitions (the sharding model), consistency (eventual vs strong), the hard limits that shape your design, and the key-overloading pattern that enables multi-entity single-table design.

## Frameworks Introduced
- **Key Overloading**: Name primary key attributes generically (`PK`, `SK`) and use prefixed values (`ORG#<name>`, `USER#<name>`) so multiple entity types coexist in one table.
  - When to use: any time you have more than one entity type and want efficient queries.
  - How: pick generic key names → prefix values by entity type → ensure uniqueness across entities → use the same pattern on GSIs.
- **TTL hygiene rule**: Never trust TTL for correctness — AWS only promises deletion within 48 hours. Always verify expiry in application code on read.

## Key Concepts
- **DynamoDB Streams**: Immutable, ordered log of every write/update/delete on a table; consumed by Lambda or other compute for fan-out/CDC.
- **TTL**: Attribute holding a Unix timestamp; DynamoDB lazily deletes items whose TTL is past. Optional per-item.
- **Partition**: A ~10GB storage node holding items hashed to a key range; three replicas (1 primary + 2 secondaries).
- **Adaptive capacity**: Auto-redistributes throughput across partitions — eliminates classic "hot partition" throughput dilution.
- **Strong consistency**: Read reflects all prior writes; opt in with `ConsistentRead=True`; uses 2× the RCU.
- **Eventual consistency**: Default; reads may go to a secondary replica that's slightly stale.
- **Hot partition**: A single partition key receiving more than 3000 RCU or 1000 WCU per second — must shard.
- **Key overloading**: Storing multiple entity types under one table using generic key names with prefixed values.

## Mental Models
- Think of a write path as: request router → hash partition key → primary node → sync replicate to one secondary → ack → async replicate to third node.
- Use the prefix in key values (`ORG#`, `USER#`) as both a type tag AND a namespace to avoid accidental overwrites between entity types.
- Treat TTL as a janitor — convenient cleanup, never a correctness boundary.
- Treat partition limits as the bound on a single "logical entity's" traffic, not the table's.

## Anti-patterns
- **Relying on TTL for security or business logic**: 48-hour deletion window is real — verify on read.
- **Unbounded one-to-many denormalization onto a single item**: 400KB item limit will eventually reject writes.
- **Picking a partition key with low cardinality or skewed distribution**: hot partition throttling.
- **Using LSI on a table that could grow >10GB per partition key**: writes start failing once the item collection hits the limit.
- **Naming primary keys after a specific entity** (`UserId`, `OrgName`): blocks key overloading and future multi-entity modeling.

## Code Examples

```text
PK                    SK                    Attributes
ORG#BERKSHIRE         ORG#BERKSHIRE         OrgName=Berkshire, Plan=...
ORG#FACEBOOK          ORG#FACEBOOK          OrgName=Facebook, Plan=...
ORG#BERKSHIRE         USER#warren           Username=warren, FirstName=Warren
ORG#BERKSHIRE         USER#charlie          Username=charlie, FirstName=Charlie
ORG#FACEBOOK          USER#mark             Username=mark, FirstName=Mark
```
- **What it demonstrates**: Key overloading — two entity types (Organization and User) sharing one table using generic `PK`/`SK` names with `ORG#`/`USER#` value prefixes. Organizations and their users land in the same item collection for efficient Query retrieval.

## Reference Tables

| Limit | Value | Why it matters |
|---|---|---|
| Max item size | **400 KB** | Pushes you to split large items; caps unbounded denormalization. |
| Query/Scan response size | **1 MB** (before FilterExpression) | Forces pagination; protects single-digit-ms latency guarantee. |
| Max RCU per partition | **3000/sec** | Hot-partition ceiling for reads; shard if exceeded. |
| Max WCU per partition | **1000/sec** | Hot-partition ceiling for writes; shard if exceeded. |
| Max item collection size (with LSI) | **10 GB** | Hard write rejection at the limit; no such limit on GSI-only tables. |
| TTL deletion SLA | within ~48 hours of TTL timestamp | Not real-time; verify on read. |
| Storage replicas | 1 primary + 2 secondaries per partition | Underlies the consistency tradeoffs. |

## Key Takeaways
1. The 400 KB item limit, 1 MB query limit, 3000 RCU / 1000 WCU per-partition limit, and 10 GB LSI item-collection limit are the four numbers every modeler must memorize.
2. Streams + Lambda is the canonical CDC pattern; use it instead of polling or external CDC tooling.
3. Eventual consistency is the default — opt into strong consistency only on the base table or an LSI, never on a GSI (it's not offered).
4. Adaptive capacity handles most hot-partition pain automatically — but it can't save you from low partition-key cardinality.
5. Key overloading (generic `PK`/`SK` with prefixed values) is the foundation of single-table design.
6. TTL is for housekeeping, not correctness — always check expiry in code.

## Connects To
- **Ch 2**: Item collections — Ch 3 explains the physical (partition) and logical (overloading) constraints behind them.
- **Ch 4**: The 1MB Query/Scan limit is what makes the API "unable to write a bad query".
- **Ch 7-8**: Single-table design depends entirely on key overloading introduced here.
- **Ch 15, 22**: Migration strategies often add new overloaded GSIs.
