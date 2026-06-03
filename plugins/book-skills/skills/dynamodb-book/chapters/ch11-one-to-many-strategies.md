# Chapter 11: Strategies for One-to-Many Relationships

## Core Idea
DynamoDB has no joins, so one-to-many relationships ("how do I fetch the parent when retrieving the related entities?") are solved by one of five named strategies. Choice depends on whether the related data is bounded, mutable, and how it is accessed.

## Frameworks Introduced

### 1. Denormalization by using a complex attribute
- **What**: Store the children as a list or map attribute on the parent item (e.g., `MailingAddresses` map on a `Customer` item).
- **When to use**: (a) you have NO access patterns that filter/query on the values inside the complex attribute, AND (b) the related data is bounded (won't blow the 400KB item limit).
- **When NOT to use**: If either answer is "yes" — you query on those values, or the count is unbounded (e.g., Order Items per Order) — pick a different strategy.

### 2. Denormalization by duplicating data
- **What**: Copy the parent's attributes onto every child item, violating second normal form (e.g., copy author bio onto each book item).
- **When to use**: The duplicated data is immutable or changes infrequently AND the number of copies is small enough to update cheaply.
- **Tradeoff**: You are balancing faster reads against update cost. Low update cost → almost any read benefit is worth it. High update cost (thousands of items, mutable data) → don't do it.

### 3. Composite primary key + the Query API action
- **What**: Use a composite PK where parent and children share the same partition key but have differently-prefixed sort keys (`ORG#Microsoft` / `METADATA#Microsoft`, `ORG#Microsoft` / `USER#Bill`). One Query returns parent + children.
- **When to use**: The most common one-to-many pattern. Good when you have multiple access patterns on the same parent/child collection (fetch parent only, parent + children, children only via `begins_with`, single child via GetItem).
- **How**: Use generic PK/SK attribute names (since multiple entity types share the table); design SK prefixes so `begins_with` isolates the entity type you need.

### 4. Secondary index + the Query API action
- **What**: Same shape as strategy 3, but the parent/child grouping lives on a GSI (`GSI1PK` / `GSI1SK`) rather than the base table's primary key.
- **When to use**: When the base table's primary key is already committed to another purpose — uniqueness enforcement, write sharding, or a deeper level of hierarchy (e.g., Tickets keyed by `TICKET#<id>` for direct lookup, then grouped under a user via GSI1).
- **Tip**: Place the parent at one end of the sort order and use `ScanIndexForward=False` to fetch parent + most-recent children.

### 5. Composite sort keys with hierarchical data
- **What**: Pack multiple hierarchy levels into the sort key separated by `#` (e.g., `STATE#CITY#ZIP`), then use `begins_with` at the granularity you want.
- **When to use**: (a) More than two levels of hierarchy AND (b) when querying a level, you actually want ALL sub-items beneath it (not just the items at that level).
- **When NOT to use**: When you query one level and don't want the sub-items (e.g., "list users" shouldn't return tickets too) — composite sort key returns too much.

## Key Concepts
- **Pre-joining**: Arranging parent and children into the same item collection at write time so a single Query emulates a SQL join with better performance.
- **Item collection**: All items sharing a partition key in a table or index — the unit a Query operates on.
- **Composite sort key (here)**: A sort key whose value is multiple hierarchy levels joined by a delimiter, enabling `begins_with` at multiple granularities.

## Mental Models
- Use strategy 1 (complex attribute) when the children are part of the parent's identity and you never need to query them independently.
- Use strategy 2 (duplicate) when the data is essentially immutable reference data (an author's birth year, a movie's release date).
- Use strategy 3 (composite PK + Query) as the default. Reach for strategy 4 (GSI) only when the base PK is taken.
- Use strategy 5 (composite SK) for geographic / org-chart / category-tree data where you legitimately want everything beneath a node.

## Anti-patterns
- **Complex attribute with unbounded children**: 400KB item ceiling will eventually break you (e.g., Order Items on an Order).
- **Duplicating mutable data across thousands of items**: Update storm + integrity risk.
- **Composite SK when you don't want sub-items**: Returns useless rows; pay RCU for nothing.
- **Interspersing high-volume children (Tickets) with low-volume children (Users) in the same item collection**: Trashes pagination for the simpler access patterns — use a GSI to separate them.

## Code Examples

```
# Strategy 3 — Composite PK + Query — SaaS Org/Users
Entity         PK              SK
Organization   ORG#Microsoft   METADATA#Microsoft
User           ORG#Microsoft   USER#bill_gates
User           ORG#Microsoft   USER#satya_nadella

# Access patterns:
# 1. GetItem PK=ORG#Microsoft, SK=METADATA#Microsoft     -> Org only
# 2. Query   PK=ORG#Microsoft                            -> Org + all Users
# 3. Query   PK=ORG#Microsoft AND begins_with(SK,"USER#") -> Users only
# 4. GetItem PK=ORG#Microsoft, SK=USER#bill_gates        -> single User
```
- **What it demonstrates**: How shared partition key + prefixed sort keys give you four access patterns from one primary key design — the canonical one-to-many pattern.

```
# Strategy 5 — Composite sort key — Starbucks by geography
PK = Country, SK = "<State>#<City>#<Zip>"
Query PK="US" AND begins_with(SK, "WA#")              -> all WA stores
Query PK="US" AND begins_with(SK, "WA#Seattle")       -> all Seattle stores
Query PK="US" AND begins_with(SK, "WA#Seattle#98101") -> all 98101 stores
```
- **What it demonstrates**: One PK + one composite SK enables filtering at four hierarchy levels with no secondary indexes.

## Reference Table — When to use which strategy

| Strategy | Use when | Avoid when | Example |
|----------|----------|------------|---------|
| Denormalize + complex attribute | Children are bounded AND never queried independently | Unbounded count, OR queried on attribute values | Customer mailing addresses |
| Denormalize + duplicate | Copied data is immutable or near-immutable; few copies | Mutable data across many items | Author bio on each Book |
| Composite PK + Query | Default; many access patterns on parent+children | When PK is needed for something else | Org + Users in SaaS |
| Secondary index + Query | Base PK already taken; deeper hierarchy | When base PK is free — use strategy 3 instead | User + Tickets via GSI1 |
| Composite sort key | >2 hierarchy levels AND you want sub-items beneath the queried level | You only want items at that level | Country/State/City/Zip |

## Key Takeaways
1. There are exactly five strategies — memorize the names; they are the vocabulary of the rest of the book.
2. Default to strategy 3 (composite PK + Query); only escalate to GSIs (strategy 4) when forced.
3. Denormalization is a feature, not a smell — it's how DynamoDB pre-joins data.
4. The two questions that gate denormalization: is it queried independently? is it bounded / immutable?
5. Tables routinely mix strategies — e.g., complex attribute for addresses, composite PK + Query for orders, GSI for tickets, in the same table.

## Connects To
- **Ch 2**: Item collections — the underlying mechanic that makes strategies 3, 4, and 5 work.
- **Ch 10**: Established why strategies matter; this chapter is the first concrete catalog.
- **Ch 12**: Many-to-many strategies — adjacency list is essentially a doubled-up version of strategy 3.
- **Ch 13**: Filtering strategies — the composite SK pattern reappears as a filtering technique.
- **Ch 18-21**: Walkthrough examples that use these strategies in combination.
