# Chapter 6: Expressions

## Core Idea
DynamoDB has five distinct expression types used across the API. Mastery of these — especially the difference between Key Condition and Filter Expressions — is foundational to correct, performant modeling. Filter Expressions in particular are widely misused.

## Frameworks Introduced
- **The Five Expression Types**: Each expression has a specific role and operates at a specific stage of the request lifecycle.
  - When to use: Every read or write API call uses at least one. Pick by operation type.
  - How: Read ops use Key Condition / Filter / Projection. Write ops use Condition. Update ops add Update Expression.

- **Query/Scan Evaluation Order**: 1) Read items matching Query/Scan, 2) Apply FilterExpression to discard non-matching items, 3) Return remaining items. The 1MB limit applies at step 1 — BEFORE filtering.

## Key Concepts
- **KeyConditionExpression**: Used only on Query; can reference only partition key (required, equality) and sort key (optional, with comparisons).
- **FilterExpression**: Post-fetch filter for Query/Scan; can reference any attribute but runs after the 1MB read.
- **ProjectionExpression**: Specifies which attributes to return; reduces bandwidth, not read cost.
- **ConditionExpression**: Asserts state of an item before a write; cancels the write if false.
- **UpdateExpression**: Specifies mutations in UpdateItem using four verbs (SET, REMOVE, ADD, DELETE).
- **Expression Attribute Names** (`#name`): Required for reserved words; safer to use everywhere.
- **Expression Attribute Values** (`:val`): Required for all expressions except ProjectionExpression.
- **Sparse Index Pattern**: REMOVE an indexed attribute to drop an item out of a GSI.
- **ConditionCheck**: Item in a TransactWriteItems that only asserts a condition without writing.

## Mental Models
- **Use KeyConditionExpression when** you need to constrain WHICH items get read (saves RCU).
- **Use FilterExpression when** you only need to trim payload size — never to substitute for a missing index.
- **Use ConditionExpression when** you'd otherwise need a read-then-write pattern; eliminates race conditions.
- **Use BETWEEN as the default sort key operator** — every other sort key condition (>, <, =, begins_with) can be expressed as BETWEEN.

## Anti-patterns
- **Using FilterExpression as a substitute for proper modeling**: It runs after the 1MB read; a 1GB table with 100KB of matching data still requires ~1000 paginated requests.
- **Assuming FilterExpression saves RCU**: It does not. You pay for everything read, even items filtered out.
- **Skipping expression attribute names for non-reserved words**: A maintenance trap. Use them uniformly.
- **Faking joins in application code** via serial Query/GetItem calls instead of designing item collections.
- **Read-then-write for uniqueness checks**: Use `attribute_not_exists(PK)` in a ConditionExpression instead.

## Code Examples
```python
# Uniqueness guard on PutItem (DeBrie says >90% of his PutItems use this)
dynamodb.put_item(
    TableName='Users',
    Item={"Username": {"S": "bountyhunter1"}, "Name": {"S": "Boba Fett"}},
    ConditionExpression="attribute_not_exists(#username)",
    ExpressionAttributeNames={"#username": "Username"}
)
```
- **What it demonstrates**: Atomic uniqueness check without a separate read; eliminates race conditions.

```python
# Atomic counter via UpdateExpression — no read needed
UpdateExpression="SET #views = #views + :inc"
```
- **What it demonstrates**: Race-free increment in a single round trip.

## Reference Tables

| Expression | API Actions | Constrains | Evaluated | Saves RCU? |
|---|---|---|---|---|
| KeyCondition | Query | Primary key only | Before read | Yes |
| Filter | Query, Scan | Any attribute | After read, before return | No |
| Projection | All reads | Attributes returned | After read | No (bandwidth only) |
| Condition | PutItem, UpdateItem, DeleteItem (+ batch/transact) | Any attribute on the item | Before write | N/A |
| Update | UpdateItem | Attribute mutations | During write | N/A |

| Update Verb | Purpose |
|---|---|
| SET | Add/overwrite attribute; arithmetic on numbers; nested map/list updates |
| REMOVE | Delete attribute; delete nested list/map property |
| ADD | Increment number; insert into set (idempotent) |
| DELETE | Remove element from a set |

| Condition Function | Use |
|---|---|
| `attribute_exists()` / `attribute_not_exists()` | Existence / uniqueness checks |
| `attribute_type()` | Type assertions |
| `begins_with()` | String prefix |
| `contains()` | Substring in string, or membership in set |
| `size()` | Length/cardinality assertions (e.g., cap a set at 10) |

## Key Takeaways
1. KeyConditionExpression is the only expression that reduces the items DynamoDB physically reads.
2. FilterExpression is a payload trimmer, not an access pattern enabler — model access patterns into your keys.
3. ConditionExpression replaces costly read-then-write patterns and prevents race conditions.
4. Three legitimate uses for FilterExpression: shrink response payload, simplify client filter logic, guard against TTL delete latency.
5. Default to BETWEEN for sort key conditions — it subsumes every other comparison.
6. Multiple update verbs in one expression: separate clauses by the verb keyword itself (no commas between verbs).
7. Sets enable idempotent ADD/DELETE operations — safe to retry.

## Connects To
- **Ch 4-5**: API call mechanics — expressions are the parameters that shape every call.
- **Ch 7**: Why filter expressions can't rescue bad modeling — access patterns must live in keys.
- **Ch 11-12**: One-to-many and filtering strategies that rely on proper KeyConditionExpression design.
- **Ch 14**: Transaction patterns using ConditionCheck for cross-item invariants.
- **Ch 17**: TTL — where FilterExpression legitimately guards against late deletions.
