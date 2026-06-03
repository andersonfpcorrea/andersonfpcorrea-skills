# Chapter 4: The Three API Action Types

## Core Idea
Every DynamoDB operation falls into one of three buckets — item-based (single item), Query (one item collection), Scan (whole table) — and the API is deliberately designed so you cannot write a query that degrades with scale.

## Frameworks Introduced
- **Three-bucket API mental model**:
  - Operating on specific items? → **item-based action** (GetItem, PutItem, UpdateItem, DeleteItem, batch/transact variants).
  - Operating on an item collection? → **Query**.
  - Operating on the whole table? → **Scan** (avoid).
- **Three rules for item-based actions**:
  1. The full primary key must be specified.
  2. All writes/updates/deletes must use item-based actions (you cannot say "update all items with partition key X").
  3. Item-based actions only run on the base table, never on a secondary index.
- **Batch vs Transaction distinction**: Batch = independent success/failure per item. Transaction = all-or-nothing rollback.

## Key Concepts
- **GetItem / PutItem / UpdateItem / DeleteItem**: The four core item-based actions.
- **BatchGetItem / BatchWriteItem**: Multiple items per round trip, independent success/failure.
- **TransactGetItems / TransactWriteItems**: Multiple items, all-or-nothing.
- **Query**: Fetch multiple items from one item collection by partition key, optionally narrowing with sort-key conditions.
- **Scan**: Read every item in the table; paginates with `LastEvaluatedKey`.
- **KeyConditionExpression**: The filter on partition key (`=` required) and optional sort key (range conditions allowed).
- **Sort-key conditions allowed in Query**: `=`, `<`, `<=`, `>`, `>=`, `between`, `begins_with()`. NOT allowed: `contains()`, `ends_with()`.
- **1MB ceiling**: All Query and Scan responses are bounded; you paginate beyond that.

## Mental Models
- Item-based = tweezers, Query = shovel, Scan = payloader.
- Use the partition key to get O(1) lookup of the right storage node; use the sort key for O(log n) B-tree traversal within the collection; then sequentially read the matching range up to 1MB.
- The API surface is small on purpose — every action that could degrade with scale was deliberately excluded.
- A Query is only "fast" because n is the size of one item collection, not the whole table.

## Anti-patterns
- **Using Scan in a latency-sensitive request path**: payloader semantics — kills scaling.
- **Trying to update many items by partition key alone**: not supported; you must enumerate full primary keys.
- **Using `contains()` or `ends_with()` in KeyConditionExpression**: the B-tree can only traverse prefixes/ranges, not suffixes.
- **Ignoring the 1MB cap and forgetting to paginate**: silent data loss when you assume "I got everything back".
- **Calling item-based actions against a secondary index**: not supported — writes go to the base table only.

## Code Examples

```python
# Query: all Tom Hanks roles whose movie title is between A and M
items = client.query(
    TableName='MoviesAndActors',
    KeyConditionExpression='#actor = :actor AND #movie BETWEEN :a AND :m',
    ExpressionAttributeNames={
        '#actor': 'Actor',
        '#movie': 'Movie'
    },
    ExpressionAttributeValues={
        ':actor': {'S': 'Tom Hanks'},
        ':a': {'S': 'A'},
        ':m': {'S': 'M'}
    }
)
```
- **What it demonstrates**: Composite primary key Query with both a partition-key equality and a sort-key range condition — the canonical "fetch many from one item collection" pattern.

```python
# Same Query against a GSI that flips partition/sort keys
items = client.query(
    TableName='MoviesAndActors',
    IndexName='MoviesIndex',
    KeyConditionExpression='#movie = :movie',
    ExpressionAttributeNames={'#movie': 'Movie'},
    ExpressionAttributeValues={':movie': {'S': 'Toy Story'}}
)
```
- **What it demonstrates**: Adding an access pattern ("all actors in a movie") by querying a GSI with inverted keys.

## Reference Tables

| Operation step | Data structure | Time complexity / cost |
|---|---|---|
| 1. Find node for partition key | Hash table | O(1) |
| 2. Find starting value for sort key | B-tree | O(log n), n = item collection size (not table) |
| 3. Read values until end of sort-key match | Sequential read | Bounded by 1MB per request |

| API action category | Examples | Operates on |
|---|---|---|
| Item-based | GetItem, PutItem, UpdateItem, DeleteItem, BatchGet/Write, TransactGet/Write | A specific item (or list of specific items) |
| Query | Query | Exactly one item collection (one partition key) |
| Scan | Scan | The whole table or index |

## Key Takeaways
1. Memorize the three buckets — every API call slots into exactly one.
2. The partition key is mandatory for item-based actions and Query — that's how O(1) shard lookup is enforced.
3. Sort-key conditions are limited to range/prefix operations because item collections are B-trees.
4. The 1MB response cap is not a bug — it's the mechanism that prevents you from writing a query that won't scale.
5. Batch ≠ transaction: batches fail per-item, transactions all-or-nothing.
6. Don't Scan in production access paths. Scans are for table exports, tiny tables, or specifically-modeled sparse indexes.
7. Item-based writes can never target a secondary index.

## Connects To
- **Ch 2**: Item collections are exactly what Query operates on.
- **Ch 3**: The 1MB limit and partition model directly explain the API's shape.
- **Ch 5**: Expression syntax (`KeyConditionExpression`, attribute names/values) is detailed in the next chapter.
- **Ch 11-13**: Modeling relationships and filtering — all built on Query semantics.
