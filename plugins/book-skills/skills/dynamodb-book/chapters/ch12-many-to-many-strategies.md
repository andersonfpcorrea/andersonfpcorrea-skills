# Chapter 12: Strategies for Many-to-Many Relationships

## Core Idea
Many-to-many is the hardest relationship to model in DynamoDB because you typically need to query both sides (students-by-class AND classes-by-student) and there are no joins. Four strategies trade off mutability, write amplification, and number of read requests.

## Frameworks Introduced

### 1. Shallow duplication
- **What**: Store a small, immutable subset of child data (just IDs and names) as a list/map on the parent. One side of the M:N is handled by a single GetItem; the other side falls back to a one-to-many strategy from Ch 11.
- **When to use**: (a) Bounded count of related entities (400KB ceiling), AND (b) only immutable fields (name, ID) need to be displayed in the parent context.
- **Example**: A `Class` item carries a `Students` list with just student names. Detail views click through to the full Student item.
- **Important**: Only handles ONE side of the relationship — solve the other side with a Ch 11 strategy.

### 2. Adjacency list
- **What**: Model both top-level entities AND the relationship itself as items. The relationship item appears in two item collections — one keyed for each side — by flipping PK/SK in a secondary index (inverted index) or by using GSI1PK/GSI1SK with flipped values.
- **When to use**: When the information about the relationship is immutable or rarely changes. Best for symmetric M:N where you query both sides equally.
- **How**:
  - Top-level item A: `PK=A#<id>, SK=A#<id>`
  - Top-level item B: `PK=B#<id>, SK=B#<id>`
  - Relationship item: `PK=A#<id>, SK=B#<id>` (lives in A's collection on base table)
  - GSI: invert so `GSI1PK=SK, GSI1SK=PK` → relationship item now lives in B's collection
- **Why it's powerful**: Mutable attributes live ONLY on the top-level items (one write to update). Immutable relationship data is duplicated only in the relationship item, which is read from both sides.

### 3. Materialized graph
- **What**: Model every node as an item collection (one node, many attribute/fact items). A secondary index reshuffles items into edge-based groupings so you can query "all people with X" or "all events on date Y".
- **When to use**: Highly-connected data with many varieties of relationships — knowledge graphs, social graphs with many edge types.
- **Caveat**: DeBrie notes this is niche — "I don't have a deeper example that shows the materialized graph in practice".

### 4. Normalization & multiple requests
- **What**: Store relationship items sparsely (just the link, no duplicated attributes), then do two-step reads: Query for the relationships, BatchGetItem to hydrate the related entities from their authoritative item.
- **When to use**: FALLBACK when the related data is highly mutable AND heavily duplicated (e.g., display names in a Twitter follow graph — millions of write fan-outs would be required to keep duplicates fresh).
- **Cost**: Two requests per read. Acceptable when write amplification of duplication would be worse.

## Key Concepts
- **Inverted index**: A GSI where the base table's PK and SK are flipped (`GSI1PK=SK, GSI1SK=PK`) — places the same item into a second item collection keyed on the other side of the relationship.
- **Pre-joining**: M:N's central challenge — arranging items at write time so a single Query returns the join.
- **Relationship item**: A first-class DynamoDB item representing the edge (e.g., a `Role` item between a `Movie` and an `Actor`).
- **Mutability is the deciding factor**: Pick adjacency list for immutable relationships, normalization for mutable ones.

## Mental Models
- "Bite the bullet and make multiple requests" — DeBrie's explicit acceptance that sometimes you cannot pre-join everything; normalization is a valid choice, not a failure.
- Shallow duplication handles ONE side. The other side still needs a one-to-many strategy.
- Adjacency list is the M:N translation of the composite-PK-plus-Query pattern — relationship items live with one parent in the base table and the other parent in the inverted GSI.

## Anti-patterns
- **Adjacency list for mutable relationship data**: Every change to the relationship requires updates in both projections; the pattern loses its appeal.
- **Duplicating highly mutable attributes (display name, profile) across millions of follower items**: Update storm; pick strategy 4 instead.
- **Trying to pre-join everything**: Some M:N relationships genuinely need two requests — don't contort the model to avoid it.

## Code Examples

```
# Adjacency list — Movies & Actors
Base table:
  PK                       SK                       Type
  MOVIE#Dark_Knight        MOVIE#Dark_Knight        Movie (mutable: boxOffice, score)
  MOVIE#Dark_Knight        ACTOR#Christian_Bale     Role  (immutable: character, year)
  MOVIE#Dark_Knight        ACTOR#Heath_Ledger       Role
  ACTOR#Christian_Bale     ACTOR#Christian_Bale     Actor (mutable: upvotes)
  ACTOR#Heath_Ledger       ACTOR#Heath_Ledger       Actor

GSI1 (inverted: GSI1PK=SK, GSI1SK=PK):
  GSI1PK                   GSI1SK                   Type
  ACTOR#Christian_Bale     ACTOR#Christian_Bale     Actor
  ACTOR#Christian_Bale     MOVIE#Dark_Knight        Role     <- same Role item, second collection
  ACTOR#Christian_Bale     MOVIE#The_Prestige       Role

# Query MOVIE#Dark_Knight on base table -> Movie + all its Roles
# Query ACTOR#Christian_Bale on GSI1   -> Actor + all his Roles
```
- **What it demonstrates**: The same Role item appears in the movie's collection (base) and the actor's collection (GSI1). One write per role; mutable Movie/Actor attributes update in only one place.

```
# Normalization & multiple requests — Twitter follows
PK                  SK
USER#alex_debrie    USER#alex_debrie                # authoritative User
USER#alex_debrie    FOLLOWING#paul_chin_jr          # sparse link
USER#alex_debrie    FOLLOWING#gillian_armstrong     # sparse link
USER#paul_chin_jr   USER#paul_chin_jr               # authoritative (display name etc.)

# Read flow:
# 1. Query PK=USER#alex_debrie -> get user + Following stubs
# 2. BatchGetItem(USER#paul_chin_jr, USER#gillian_armstrong) -> hydrate
```
- **What it demonstrates**: Two-step read pattern — Query for relationships, BatchGet for authoritative data. Each Following item is tiny; updates to a display name touch ONE item regardless of follower count.

## Reference Table — When to use which strategy

| Strategy | Mutability of relationship/related data | Read cost | Write cost | Example |
|----------|----------------------------------------|-----------|------------|---------|
| Shallow duplication | Immutable subset only | 1 GetItem | High if data changes | Class with student names |
| Adjacency list | Relationship is immutable | 1 Query per side | 1 write per role | Movies & Actors with Roles |
| Materialized graph | Varied; flexible | 1 Query per relationship type | Multi-item per node | Knowledge graph |
| Normalization + multiple requests | Highly mutable | 2 requests (Query + BatchGet) | 1 write per change | Twitter followers |

## Key Takeaways
1. Four strategies — and the choice is driven primarily by MUTABILITY of the related/relationship data.
2. Adjacency list is the most-cited single-table pattern in the book — memorize the inverted-index structure.
3. Shallow duplication only handles one side; combine with a Ch 11 strategy for the other side.
4. Multiple requests are a legitimate strategy, not a failure — sometimes pre-joining is more expensive than the read amplification you're trying to avoid.
5. The relationship itself can (and often should) be its own item — `Role`, `Following`, `Enrollment` are first-class entities.

## Connects To
- **Ch 11**: Adjacency list builds on composite-PK-plus-Query and secondary-index-plus-Query.
- **Ch 13**: Sparse index pattern appears here implicitly — the Following items are sparse relationship records.
- **Ch 14**: Sorting strategies — adjacency list's inverted index is a form of sort manipulation.
- **Ch 19-21**: GitHub and e-commerce examples use adjacency list extensively.
