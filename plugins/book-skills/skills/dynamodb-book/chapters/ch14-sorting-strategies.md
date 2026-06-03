# Chapter 14: Strategies for Sorting

## Core Idea
All sorting in DynamoDB happens through the sort key of an item collection. Because items in a partition are stored as a B-tree ordered lexicographically (UTF-8 bytes) on the sort key, you must arrange your items so they are *already sorted* in the order you need them — there is no `ORDER BY` clause at query time. Two prerequisites: use a composite primary key, and do every ordering decision via the sort key.

## Frameworks Introduced

- **Basics of sorting (lexicographic order)**: Sort keys are scalars (string, number, binary). Strings/binary sort by UTF-8 bytes; numbers sort numerically.
  - When to use: every model — you must understand this before designing anything.
  - How: standardize string sort keys to a single case (UPPER or lower) because uppercase bytes precede lowercase; for timestamps pick a sortable format (epoch number or ISO-8601), never display formats like "May 26, 1988"; for unique-but-sortable IDs prefer **KSUIDs** (27-char, time-prefixed, more unique than UUIDv4) over UUIDv4.

- **Sorting on changing attributes**: Never put a frequently-changing attribute into your primary key.
  - When to use: leaderboards, "most recently updated" feeds, anything sorted by a mutable value (score, `UpdatedAt`).
  - How: keep the base table primary key on immutable attributes (e.g. `OrgName` + `TicketId`). Project the changing sort attribute (e.g. `UpdatedAt`) into a **GSI** sort key. DynamoDB handles the replicated re-sort; you avoid the delete-then-create dance (and the data-loss risk) of mutating a primary key.

- **Ascending vs. descending**: `ScanIndexForward=True` (default) reads ascending; `False` reads descending.
  - When to use: any time you need "most recent N" or "top N".
  - How: when co-locating a parent with related items (one-to-many in one item collection), prefix related-item sort keys so the parent sits at the *end* you'll read from. E.g. prefix Reading SKs with `#` so they sort before `DEVICE#...`, then query with `ScanIndexForward=False` to get device + most recent readings in one shot.

- **Two relational access patterns in a single item collection**: Pack two one-to-many relationships into one partition by placing the parent in the middle.
  - When to use: parent has two child entity types, each needing "fetch parent + children" access, and you want to avoid an extra GSI.
  - How: sort one child type *before* the parent (e.g. `#TEAM#...`) and the other *after* (e.g. `USER#...`). Query with `SK <= :parentSk` + `ScanIndexForward=False` for one direction; `SK >= :parentSk` forward for the other.

- **Zero-padding with numbers**: When numeric IDs live inside a string sort key, lexicographic order puts `"10"` before `"2"`. Left-pad to fixed width.
  - When to use: any string sort key embedding an integer (reading numbers, issue numbers, sequence IDs).
  - How: pick a width well beyond your realistic max (DeBrie: imagined max + 2-3 extra digits), then alert when usage exceeds ~30% of capacity.

- **Faking ascending order (zero-padded difference)**: Store `max - n` instead of `n` to invert sort direction in place.
  - When to use: parent has two child types both keyed by numbers, and you want both fetched in the *same* direction off the parent without a second GSI.
  - How: encode one child's sort suffix as `(MAX - id)` zero-padded. Reading forward from the parent then yields descending-by-id.

## Key Concepts
- Sort key is the *only* mechanism for ordering query results.
- Lexicographic ordering: uppercase before lowercase; digits compared char-by-char left to right.
- Primary key attributes are immutable on `UpdateItem` — design around this.
- GSIs absorb churn from changing attributes for free (no application-level delete+create).

## Mental Models
- The sort key is a B-tree cursor: you pick a starting point and a direction.
- A `#` prefix is your "sort me first" lever; absence of prefix is your "sort me later" lever.
- Sorting is a *write-time* decision in DynamoDB, not a read-time one.

## Anti-patterns
- Putting `UpdatedAt`, score, or any mutable attribute in the base table primary key.
- Using display-format timestamps (`"May 26, 1988"`) as sort keys.
- Forgetting case normalization — `"DeBrie"` will sort before `"Dean"`.
- Embedding raw integers in string sort keys without zero-padding.
- Reaching for a new GSI before checking whether the parent can be repositioned in an existing item collection.

## Code Examples

```python
# Zero-padding so lexicographic order matches numeric order
reading_number = 10
sk = f"READING#{reading_number:05d}"   # "READING#00010"

# Zero-padded difference to fake ascending while reading ascending
MAX = 99999
reading_id = 157
sk = f"READING#{MAX - reading_id:05d}" # "READING#99842"
```
- **What it demonstrates**: width-5 padding handles up to 99,999 items; the difference trick lets a single item collection serve two child types in the same scan direction.

```python
# Two relational patterns in one collection: fetch Org + Teams (descending)
result = dynamodb.query(
    TableName='SaaSTable',
    KeyConditionExpression="#pk = :pk AND #sk <= :sk",
    ExpressionAttributeNames={"#pk": "PK", "#sk": "SK"},
    ExpressionAttributeValues={
        ":pk": {"S": "ORG#MCDONALDS"},
        ":sk": {"S": "ORG#MCDONALDS"},
    },
    ScanIndexForward=False,
)
```
- **What it demonstrates**: parent sits between two child types; `<=` plus reverse scan picks up parent + Teams in one Query; mirror with `>=` + forward scan for Users.

## Reference Tables

| Strategy | Notes |
|---|---|
| KSUIDs for sortable unique IDs | 27-char, time-prefixed; better than UUIDv4 when chronological sort matters |
| Sorting on changing attributes | Use a GSI; keep base PK immutable |
| Ascending vs. descending | Position parent based on the direction you'll read |
| Two relational patterns in one collection | Parent in middle, children prefixed differently |
| Zero-padding | Fixed width, sized for max + headroom |
| Faking ascending order | Store `MAX - n` zero-padded |

## Key Takeaways
- Sort key is everything; choose immutable attributes for the base table and project mutable ones into GSIs.
- Lexicographic ordering plus a `#` prefix is a remarkably expressive sort-direction tool.
- Zero-padding is non-negotiable for numeric values in string sort keys.
- Combining basic primitives (padding, prefix, `ScanIndexForward`, difference encoding) lets one item collection do work that naively requires multiple GSIs.

## Connects To
- Chapter 4 (partition + B-tree storage internals)
- Chapter 5 (Query API, `ScanIndexForward`)
- Chapter 9 (one-to-many strategies; indexing vs. application attributes)
- Chapter 11-13 (relationships and filtering strategies that depend on sort order)
- Chapter 17, 19, 21 (worked examples applying these patterns)
