# Chapter 16: Additional Strategies

## Core Idea
A grab-bag of indispensable patterns that don't fit cleanly into relationships, filtering, sorting, or migrations. Each one solves a recurring real-world need — multi-attribute uniqueness, sequential IDs, pagination, global state, and reference counts — using DynamoDB primitives (condition expressions, transactions, atomic counters, `LastEvaluatedKey`) in idiomatic combinations.

## Frameworks Introduced

- **Ensuring uniqueness on two or more attributes**: Uniqueness in DynamoDB is enforced only by the primary key, and a *composite* PK only enforces uniqueness of the *combination*. To guarantee independent uniqueness of two attributes (e.g. username AND email), write a **tracking item** per attribute inside a `TransactWriteItems` call, each with a `attribute_not_exists(PK)` condition.
  - When to use: signups (unique username + unique email), any "no duplicates on field X *and* field Y" rule.
  - How: one Put for the canonical user item (`PK=USER#<username>`), one Put for an email marker item (`PK=USEREMAIL#<email>`), both in one transaction. If you also need to *look up* by email, duplicate the user data onto the email item — but then every user update becomes a transaction too (avoid if possible).

- **Handling sequential IDs**: DynamoDB has no built-in auto-increment. Combine an atomic counter on a parent item with a follow-up Put.
  - When to use: human-facing sequential numbers (Jira issue numbers, GitHub issue numbers, order numbers).
  - How: `UpdateItem` with `SET IssueCount = IssueCount + 1` and `ReturnValues='UPDATED_NEW'` on the parent Project item, read the returned value, then `PutItem` the new child using that number. Two round trips per create — accept the cost when sequential IDs are a true requirement.

- **Pagination**: DynamoDB returns `LastEvaluatedKey` on truncated results; pass it back as `ExclusiveStartKey` on the next call. Surface it in URLs so the client carries cursor state.
  - When to use: any list endpoint that returns more than one page of items from an item collection.
  - How: query within an item collection with `Limit=N`. Encode the last-seen sort-key value (e.g. an OrderId) into a URL query parameter like `?before=<orderId>`. On the next request, build the Query's sort-key condition from that cursor (`SK < :sk` with `:sk = "ORDER#<lastOrderId>"`). No OFFSET/LIMIT; no skip-N performance cliff.

- **Singleton items**: A single item per table that holds global state across the application.
  - When to use: global limits ("at most 100 jobs running"), feature-flag-style toggles, an assembled "front page" view of curated content, any concept that has exactly one instance.
  - How: pick a fixed PK/SK like `PK=JOBS, SK=JOBS`. Mutate it transactionally alongside the real entity (e.g. add a job id to a string set on the singleton with a length-check condition, while updating the Job item's status).

- **Reference counts**: Maintain a denormalized count on the parent so the UI doesn't pay to count children.
  - When to use: tweet retweets, GitHub stars, comment counts — anywhere the UI shows "N likes" without listing them.
  - How: in a `TransactWriteItems`, (1) Put the related child item with `attribute_not_exists(PK)` to enforce idempotency (a user can't double-star), (2) UpdateItem on the parent with `SET StarCount = StarCount + 1` and `attribute_exists(PK)` to ensure the parent still exists. Transactions make this safe; without them you'd hand-roll rollback logic.

## Key Concepts
- Condition expressions (`attribute_not_exists`, `attribute_exists`) are how you express integrity constraints.
- `TransactWriteItems` is the building block for multi-item invariants.
- Atomic counters via `SET x = x + :incr` are race-safe.
- `LastEvaluatedKey` / `ExclusiveStartKey` are DynamoDB's only pagination cursor — there is no OFFSET.
- "Tracking items" (uniqueness markers, singletons) are valid first-class entities even with no business data.

## Mental Models
- Uniqueness = primary key. Need uniqueness on field X? Create an item whose PK is X.
- Sequential IDs = atomic counter on the parent + child Put — two trips, accept the cost.
- Pagination cursor = the last seen sort key, period.
- Singleton = "this concept has one instance; give it a fixed PK."
- Reference count = a transaction with one Put and one UpdateItem.

## Anti-patterns
- Putting both `username` and `email` into a single composite PK and assuming both are unique — only the *combination* is.
- Counting children at read time with a Query + length — burns RCUs proportional to child count for a single integer.
- Pagination via `OFFSET`-style logic (DynamoDB has none) or scanning the whole collection client-side.
- Duplicating user data onto a uniqueness-tracking item when you only ever query by the canonical key — wastes writes.
- Manual multi-step rollbacks for "increment count when item is added" — use a transaction.

## Code Examples

```python
# Two-attribute uniqueness: username AND email
response = client.transact_write_items(
    TransactItems=[
        {
            "Put": {
                "TableName": "UsersTable",
                "Item": {
                    "PK": {"S": "USER#alexdebrie"},
                    "SK": {"S": "USER#alexdebrie"},
                    "Username": {"S": "alexdebrie"},
                    "FirstName": {"S": "Alex"},
                },
                "ConditionExpression": "attribute_not_exists(PK)",
            }
        },
        {
            "Put": {
                "TableName": "UsersTable",
                "Item": {
                    "PK": {"S": "USEREMAIL#alex@debrie.com"},
                    "SK": {"S": "USEREMAIL#alex@debrie.com"},
                },
                "ConditionExpression": "attribute_not_exists(PK)",
            }
        },
    ]
)
```
- **What it demonstrates**: each Put has its own `attribute_not_exists` guard; the transaction fails atomically if either constraint is violated, so the user is never half-created.

```python
# Sequential ID via atomic counter + PutItem
resp = client.update_item(
    TableName="JiraTable",
    Key={"PK": {"S": "PROJECT#my-project"}, "SK": {"S": "PROJECT#my-project"}},
    UpdateExpression="SET #count = #count + :incr",
    ExpressionAttributeNames={"#count": "IssueCount"},
    ExpressionAttributeValues={":incr": {"N": "1"}},
    ReturnValues="UPDATED_NEW",
)
current_count = resp["Attributes"]["IssueCount"]["N"]

client.put_item(
    TableName="JiraTable",
    Item={
        "PK": {"S": "PROJECT#my-project"},
        "SK": {"S": f"ISSUE#{current_count}"},
        "IssueTitle": {"S": "Build DynamoDB data model"},
    },
)
```
- **What it demonstrates**: the increment is race-safe; `ReturnValues='UPDATED_NEW'` hands back the post-increment value to use as the new issue's identifier.

```python
# Reference count: add a Star, increment StarCount, atomically
result = dynamodb.transact_write_items(
    TransactItems=[
        {
            "Put": {
                "TableName": "GitHubModel",
                "Item": {
                    "PK": {"S": "REPO#alexdebrie#dynamodb-book"},
                    "SK": {"S": "STAR#danny-developer"},
                },
                "ConditionExpression": "attribute_not_exists(PK)",
            }
        },
        {
            "Update": {
                "TableName": "GitHubModel",
                "Key": {
                    "PK": {"S": "REPO#alexdebrie#dynamodb-book"},
                    "SK": {"S": "#REPO#alexdebrie#dynamodb-book"},
                },
                "ConditionExpression": "attribute_exists(PK)",
                "UpdateExpression": "SET #count = #count + :incr",
                "ExpressionAttributeNames": {"#count": "StarCount"},
                "ExpressionAttributeValues": {":incr": {"N": "1"}},
            }
        },
    ]
)
```
- **What it demonstrates**: idempotency on the Star (a user can star only once) plus parent-existence check plus atomic counter increment, all-or-nothing.

## Reference Tables

| Strategy | Mechanism | Cost |
|---|---|---|
| Multi-attribute uniqueness | Tracking item per attribute in `TransactWriteItems` | 2x writes (1 transaction) |
| Sequential IDs | Atomic counter on parent + Put | 2 round trips per create |
| Pagination | `LastEvaluatedKey` / `ExclusiveStartKey` in URL | 0 extra |
| Singleton items | Fixed PK/SK; transactional updates | 1 extra item in transaction |
| Reference counts | Transaction: Put child + Update parent count | 1 transaction per write |

## Key Takeaways
- DynamoDB's primitives compose: condition expressions + transactions + atomic counters cover most integrity needs.
- Tracking items (uniqueness markers, singletons) are legitimate modeling tools, not hacks.
- Always denormalize counts onto the parent; never count by querying children.
- Pagination is cursor-based — design URLs to carry sort-key cursors.

## Connects To
- Chapter 4 (TransactWriteItems semantics, condition expressions)
- Chapter 6 (UpdateItem, atomic counters, `ReturnValues`)
- Chapter 9 (denormalization principles)
- Chapter 11 (one-to-many — reference counts live on the parent)
- Chapters 19, 20 (E-commerce, Big Time Deals worked examples apply uniqueness, singletons, reference counts)
