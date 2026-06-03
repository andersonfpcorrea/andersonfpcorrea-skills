# Chapter 5: Using the DynamoDB API

## Core Idea
Working effectively with the DynamoDB API comes down to three habits: master expression attribute names/values, skip ORMs/ODMs in favor of thin SDK helpers, and exploit the optional request properties (`ConsistentRead`, `ScanIndexForward`, `ReturnValues`, `ReturnConsumedCapacity`, `ReturnItemCollectionMetrics`).

## Frameworks Introduced
- **Expression placeholder rule**: Always pass attribute values via `ExpressionAttributeValues` (required for type encoding) and prefer `ExpressionAttributeNames` for any attribute reference to avoid reserved-word collisions and nested-attribute parsing issues.
- **No-ODM policy with two exceptions**: Don't use an ODM. The only acceptable helpers are (1) AWS Document Client (Node.js) / Boto3 Table resource (Python) — thin attribute-type wrappers, and (2) DynamoDB Toolbox-style entity helpers that don't hide the API.

## Key Concepts
- **ExpressionAttributeValues** (`:foo`): Required for safely passing typed values into expressions. Each value carries its DDB type tag (`S`, `N`, `B`, etc.).
- **ExpressionAttributeNames** (`#foo`): Aliases for attribute names. Required when an attribute name is a reserved word, contains a period, or addresses a nested map.
- **Reserved words**: 573 of them in DynamoDB — common ones include `Bucket`, `By`, `Count`, `Month`, `Name`, `Timestamp`, `Timezone`.
- **ConsistentRead**: Available on GetItem, BatchGetItem, Query, Scan. Doubles RCU cost. Not allowed on GSIs.
- **ScanIndexForward**: Boolean on Query only; `False` = read sort key in descending order.
- **ReturnValues**: Optional on PutItem, UpdateItem, DeleteItem, TransactWriteItem. Controls what the response includes about the modified item.
- **ReturnConsumedCapacity**: `NONE | TOTAL | INDEXES` — surface the RCU/WCU billed for the request.
- **ReturnItemCollectionMetrics**: On write APIs; returns approximate item-collection size — primarily useful as an early-warning when nearing the 10GB LSI limit.
- **Document Client (Node.js)** / **Table resource (Boto3)**: Auto-infers attribute types so you write plain language values, not `{'S': ...}` blobs.

## Mental Models
- Treat expressions as parameterized SQL — never inline values, always bind via `:placeholder` with typed values.
- Reach for `ScanIndexForward=False` whenever your access pattern is "most recent N" — read the head of the B-tree backwards instead of paging through everything.
- Use `ReturnValues` to fold a read into a write (e.g., increment and return the new counter in one round trip).
- Use `ReturnConsumedCapacity` during data modeling to validate which access patterns are expensive before traffic gets there.
- Use `ReturnItemCollectionMetrics` as a tripwire — alert at ~7-8 GB so you have runway before writes start failing at 10 GB.

## Anti-patterns
- **Inlining attribute values into expression strings**: forces the server to parse types, breaks on nested maps.
- **Skipping `ExpressionAttributeNames` because "my attribute name looks normal"**: 573 reserved words; just use the alias.
- **Using a full ODM**: pushes you into incorrect (relational-shaped) data modeling and saves little code.
- **Forgetting `ScanIndexForward=False` for "most recent" patterns**: silently 1000x the read cost on time-series data.
- **Requesting `ConsistentRead=True` against a GSI**: not supported and will error.

## Code Examples

```python
# Expression with both names and values
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
- **What it demonstrates**: Canonical use of `#name` aliases plus typed `:value` placeholders.

```javascript
// Bare AWS SDK — verbose typed attributes
const dynamodb = new AWS.DynamoDB()
await dynamodb.putItem({
  TableName: 'MoviesAndActors',
  Item: {
    Actor: { 'S': 'Tom Hanks' },
    Movie: { 'S': 'Forrest Gump' },
    Role:  { 'S': 'Forrest Gump' },
    Year:  { 'S': '1994' },
    Genre: { 'S': 'Drama' }
  }
}).promise()

// Document Client — types inferred
const docClient = new AWS.DynamoDB.DocumentClient()
await docClient.put({
  TableName: 'MoviesAndActors',
  Item: {
    Actor: 'Tom Hanks',
    Movie: 'Forrest Gump',
    Role:  'Forrest Gump',
    Year:  '1994',
    Genre: 'Drama'
  }
}).promise()
```
- **What it demonstrates**: The acceptable abstraction — Document Client strips attribute-type boilerplate without hiding the API surface.

## Reference Tables

| Property | Available on | Purpose |
|---|---|---|
| `ConsistentRead` | GetItem, BatchGetItem, Query, Scan | Opt into strong consistency (2× RCU). Not on GSI. |
| `ScanIndexForward` | Query only | `False` reads sort key descending — essential for "most recent N". |
| `ReturnValues` | PutItem, UpdateItem, DeleteItem, TransactWriteItem | Return item state before/after the write. |
| `ReturnConsumedCapacity` | All read/write APIs | Surface billed RCU/WCU (`NONE`/`TOTAL`/`INDEXES`). |
| `ReturnItemCollectionMetrics` | Write APIs | Approximate collection size — tripwire for LSI 10GB limit. |

| `ReturnValues` value | Behavior |
|---|---|
| `NONE` (default) | Return nothing about the item |
| `ALL_OLD` | Item as it was before the operation |
| `UPDATED_OLD` | Just the changed attributes, before |
| `ALL_NEW` | Item as it looks after the operation |
| `UPDATED_NEW` | Just the changed attributes, after |

## Key Takeaways
1. Always use `:value` placeholders bound through `ExpressionAttributeValues` — they encode the attribute's type.
2. Default to using `#name` aliases too — 573 reserved words make this the safer habit.
3. Don't use an ODM. Use Document Client (Node) or Boto3 `Table` (Python) for type-inference boilerplate relief — nothing heavier.
4. Use `ScanIndexForward=False` for any "latest N" pattern on a composite key.
5. `ReturnValues` lets you collapse read-then-write round trips for counters and conditional updates.
6. `ReturnItemCollectionMetrics` is your early-warning system for the 10GB LSI limit.
7. Strong consistency is opt-in (`ConsistentRead=True`) and never available on GSIs.

## Connects To
- **Ch 3**: ConsistentRead, GSI vs LSI consistency, and the LSI 10GB limit (surfaced via item-collection metrics) all stem from Ch 3 concepts.
- **Ch 4**: This chapter operationalizes the API actions cataloged in Ch 4.
- **Ch 6**: Expressions get a full deep-dive in the next chapter (Condition, Update, Filter, Projection expressions).
- **Ch 11, 16, 21**: `ScanIndexForward` and `ReturnValues` patterns reappear in one-to-many strategies, auto-incrementing integers, and the GitHub walkthrough.
