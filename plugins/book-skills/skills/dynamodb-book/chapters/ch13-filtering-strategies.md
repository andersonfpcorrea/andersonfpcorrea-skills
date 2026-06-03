# Chapter 13: Strategies for Filtering

## Core Idea
"Filtering in DynamoDB is almost exclusively focused on your primary key." There are six strategies, but five of them are ways to design your PK/SK (on base table OR secondary index) so that a Query returns only the items you want. FilterExpressions, despite the name, do NOT save read capacity — they run AFTER items are fetched.

## Frameworks Introduced

### 1. Filtering with the partition key
- **What**: Use the PK to narrow from terabytes to a single 10GB storage node in O(1). The mandatory starting point for every filter (except the dreaded Scan).
- **When to use**: Always — it is the precondition for all other filtering. Make sure items you fetch together share a partition key.
- **Reminder**: Every DynamoDB API except Scan requires a partition key in the request.

### 2. Filtering with the sort key
Two sub-strategies:

**2a. Simple filters with the sort key** — sort key has inherent meaning (date, score). Use `BETWEEN`, `<`, `>=`, `begins_with`. Example: orders for a customer in a date range.

**2b. Assembling different collections of items** — sort key has no inherent meaning but is engineered so that ordering plus `<=` / `>=` carves the item collection. Example: `ISSUE#...` < `REPO#...` < `STAR#...` — query `SK <= REPO#...` to fetch Repo + Issues; `SK >= REPO#...` to fetch Repo + Stars.

- **When to use**: Works on base table OR any GSI with a composite key. Requires a composite primary key.

### 3. Composite sort key
- **What**: Combine two attribute values in the sort key separated by `#` (e.g., `OrderStatusDate = "CANCELLED#2019-07-01T..."`), put it on a secondary index, then Query with PK = customer AND SK BETWEEN `CANCELLED#<start>` AND `CANCELLED#<end>`.
- **When to use**: (a) You always filter on two-or-more attributes together AND (b) one of them is an enum-like value (small set of distinct values).
- **Order matters**: The enum-like attribute MUST come FIRST in the composite. Reversing (`<date>#<status>`) intersperses items by the high-cardinality field and makes the enum filter useless.

### 4. Sparse indexes  (HIGH-IMPORTANCE PATTERN)
- **What**: A secondary index where only items containing the index's key attributes get projected. Items missing those attributes are excluded entirely — the index is intentionally sparse.
- **Why it matters**: This is one of the most powerful tools in DynamoDB modeling. It lets you turn a Scan-like global filter into a tiny, targeted Query or Scan over a pre-filtered subset.
- **When to use**:
  - **4a. Global filter on an item type** — add the index attribute ONLY to items matching a predicate (e.g., only Admins get `GSI1SK=Admin`). Query the index to find all Admins in an Org without scanning.
  - **4b. Project a single entity type into an index** — give one entity type a unique attribute like `CustomerIndexId` so ONLY that type is projected. Scan the GSI to find all Customers efficiently. Note: this does NOT combine with index overloading.
- **Mental model**: "DynamoDB only copies an item into a GSI if the item has the GSI's key attributes." Weaponize the absence.

### 5. Filter Expressions
- **What**: An expression applied to Query/Scan results that removes non-matching items.
- **CRITICAL WARNING**: Filter expressions are applied AFTER items are read. You PAY for everything filtered out, and the 1MB result limit is evaluated BEFORE the filter runs. "You cannot count on filter expressions to save a bad model. Filter expressions are, at best, a way to slightly improve the performance of a data model that already works well."
- **Acceptable uses**:
  1. Reducing response payload size (less wire data after fetch).
  2. Easier client-side code (personal preference).
  3. **TTL safety net** — TTL deletion can lag up to 48 hours; filter expressions exclude items that should have expired.
- **DeBrie's rule**: Acceptable only if you get a 30-40%+ hit rate OR the pre-filter result is well under 1MB (ideally <100KB).
- **Pagination trap**: Filter expressions break `Limit=N` semantics — you can no longer be sure you'll get N items back, leading to overfetching or follow-up requests.

### 6. Client-side filtering
- **What**: Fetch a small dataset, filter in the browser/client. "Not a DynamoDB filter strategy at all."
- **When to use**:
  1. When filtering is awkward to model in DDB (e.g., "find gaps in a calendar").
  2. When the dataset is small (sub-1MB) and the user needs many flexible filter/sort columns.
- **Rick Houlihan**: "The browser is sitting in a 99% idle loop. Give it something to do!"
- **When NOT to use**: Large datasets (Amazon.com's full inventory — too broad).

## Key Concepts
- **Composite sort key (vs. composite primary key)**: Composite primary key = PK + SK (a technical term). Composite sort key = a SORT KEY VALUE containing multiple data elements concatenated (a modeling term). DeBrie explicitly flags this confusion.
- **Sparse index**: A GSI that excludes items not carrying its key attributes. Used to pre-filter a global slice of the table.
- **Overloaded sparse index**: Many overloaded GSIs are technically sparse, but DeBrie reserves "sparse index" for the case where sparseness is the intentional design lever.
- **1MB barrier**: Both Query and Scan return at most 1MB before any FilterExpression runs.

## Mental Models
- Filtering = primary key design. If you cannot filter with PK/SK, your model is probably wrong.
- Use FilterExpression for trimming, never for selecting. If you need it to find your items, redesign.
- Sparse indexes turn "give me a rare subset" into a cheap O(small) query.
- "Where can I push the filter?" — to the partition key first, then the sort key, then a secondary index, and only as a last resort to a FilterExpression or the client.

## Anti-patterns
- **Using FilterExpression to find items (instead of trim them)**: You will pay RCU for every filtered-out item and may hit the 1MB cap before getting any matches. THE chapter's primary warning.
- **Composite sort key with high-cardinality field first**: `<date>#<status>` intersperses by date, defeats the status filter.
- **Sparse index combined with overloading the index for other entity types** (in pattern 4b): Breaks "single entity type in the index" benefit.
- **Relying on `Limit=N` with a FilterExpression**: You cannot promise the client N items.
- **Reaching for Scan to find an entity type**: Use sparse index 4b instead.

## Code Examples

```python
# Strategy 2a — Simple sort key filter (date range)
result = dynamodb.query(
    TableName='CustomerOrders',
    KeyConditionExpression="#c = :c AND #ot BETWEEN :start and :end",
    ExpressionAttributeValues={
        ":c":     {"S": "36ab55a589e4"},
        ":start": {"S": "2020-01-11T00:00:00.000000"},
        ":end":   {"S": "2020-02-01T00:00:00.000000"}
    }
)
```
- **What it demonstrates**: Sort key with inherent meaning (timestamp) + BETWEEN — the cleanest filtering pattern.

```python
# Strategy 3 — Composite sort key (enum + date)
# Sort-key attribute: OrderStatusDate = "CANCELLED#2019-07-15T..."
result = dynamodb.query(
    TableName='CustomerOrders',
    IndexName="OrderStatusDateGSI",
    KeyConditionExpression="#c = :c AND #osd BETWEEN :start and :end",
    ExpressionAttributeValues={
        ":c":     {"S": "2b5a41c0"},
        ":start": {"S": "CANCELLED#2019-07-01T00:00:00.000000"},
        ":end":   {"S": "CANCELLED#2019-10-01T00:00:00.000000"}
    }
)
```
- **What it demonstrates**: Two-attribute filter (status + date range) using one sort key on a GSI — exact match on the enum, range on the date.

```
# Strategy 4a — Sparse index for global filter on an item type
# Only Admin users carry GSI1SK="Admin"; regular Members omit it.
Base table:
  PK             SK              Role     GSI1PK         GSI1SK
  ORG#Berk       USER#Buffett    Admin    ORG#Berk       Admin
  ORG#Berk       USER#Munger     Member   (no GSI1SK)    (no GSI1SK)
  ORG#Berk       USER#Sandberg   Admin    ORG#Berk       Admin

# GSI1: Query GSI1PK=ORG#Berk -> only Admins are returned (Munger is excluded entirely)
```
- **What it demonstrates**: The "weaponize absence" sparse-index trick — Munger's User item never appears in GSI1 because his item lacks GSI1SK.

## Reference Table — When to use which strategy

| Strategy | Where filtering happens | Cost characteristics | Use when |
|----------|-------------------------|----------------------|----------|
| Partition key | DynamoDB router | O(1), free | Always — mandatory baseline |
| Sort key (simple or assembled) | Single storage node | Pay only for matched range | You have a composite key and need to slice the item collection |
| Composite sort key | Secondary index | Pay only for matched range | Always filter on 2 attrs together AND one is enum-like |
| **Sparse index** | Secondary index | Pay only for projected items | Need a global filter on a rare subset, OR want to find all items of one entity type |
| Filter expression | Client side of DDB (after fetch) | PAY for filtered-out items | Trimming a narrow result set (30-40%+ hit rate, <1MB) |
| Client-side filtering | Application | Pay for full fetch | Small dataset, flexible/multi-column UI filtering |

## Key Takeaways
1. Filtering in DynamoDB IS primary key design — the FilterExpression is a finishing tool, not a selection mechanism.
2. Sparse indexes are DeBrie's favorite pattern in this chapter — master both flavors (global filter, single-entity projection).
3. FilterExpression applied AFTER fetch is the #1 misconception of DDB beginners — they don't save RCU and don't help you hit `Limit=N`.
4. Composite sort key needs the enum field FIRST; otherwise the high-cardinality field defeats your filter.
5. Client-side filtering is a legitimate, often-overlooked tool — leverage the idle browser when datasets are small.
6. There are exactly six filtering strategies; if you find yourself reaching for a seventh, you probably need to redesign your PK/SK instead.

## Connects To
- **Ch 5**: FilterExpression mechanics — chapter 13 builds on the warning introduced there.
- **Ch 11**: Composite sort key pattern (strategy 5 there) reappears here as filtering strategy 3.
- **Ch 12**: Adjacency list relies on the inverted-index idea similar to sparse-index strategies.
- **Ch 14**: Sorting strategies — many filtering decisions force sorting decisions.
- **Ch 18-21**: Worked examples use sparse indexes and composite sort keys repeatedly.
