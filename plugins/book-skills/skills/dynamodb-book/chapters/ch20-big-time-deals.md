# Chapter 20: Building Big Time Deals

## Core Idea
A deals/coupons mobile app (Slickdeals-style) with 23 access patterns, dual audiences (external deal-hunters and internal editors), and several "fetch latest" patterns that would naively create hot or fat partitions. The walkthrough showcases time-bucket sharding, singleton items, read-shard caches inside DynamoDB itself, and two distinct sparse-index strategies.

## Domain & Entities
Entities: `Deal`, `Brand`, `Category` (only 8, fixed), `FeaturedDeal`, `Page` (Front Page, Editor's Choice), `User`, `Message`. Plus derived/helper items: `Brands` singleton, `BrandLike`, `BrandWatch`, `CategoryLike`, `CategoryWatch`, `FrontPage`/`EditorsChoice` singletons, and optional `DealsCache` shards. A Deal belongs to one Brand and one Category. Users like/watch brands and categories (many-to-many) and receive in-app Messages either from watch notifications or from editor blasts.

## Access Patterns
Grouped into the categories DeBrie uses:

**Create core entities**
- **Create Deal / Create Brand / Create Category**: internal CMS PutItems; brand creation also updates the `BRANDS` singleton's set attribute.

**Set Featured Deals (Front Page / Category / Editor's Choice)**
- All three: CMS overwrites the relevant singleton or Category item with the full list of featured deals (denormalized as a complex attribute).

**Fetch pages**
- **Fetch Front Page & Latest Deals**: GetItem on `FRONTPAGE` singleton + Query GSI1 on `DEALS#<TruncatedTimestamp>` for the latest 25 deals.
- **Fetch Category & Latest Deals**: GetItem on Category item + Query GSI3 on `CATEGORY#<Cat>#<TruncatedTimestamp>`.
- **Fetch Latest Deals for Brand**: Query GSI2 on `BRAND#<Brand>#<TruncatedTimestamp>`.
- **Fetch Editor's Choice Page**: GetItem on `EDITORSCHOICE` singleton (all featured deals embedded).
- **Fetch all Brands**: GetItem on the `BRANDS` singleton (a set of brand names).
- **Fetch Deal**: GetItem on `DEAL#<DealId>`.

**Users and interactions**
- **Create User**: PutItem with `attribute_not_exists(PK)` for username uniqueness.
- **Like Brand / Watch Brand / Like Category / Watch Category for User**: TransactWriteItems — create the like/watch tracking item (condition: not already exists) AND increment `LikesCount`/`WatchCount` on the parent Brand/Category item.

**Messages**
- **View Messages for User**: Query main table on `PK = MESSAGES#<Username>`, descending.
- **View Unread Messages for User**: same Query but against GSI1 (sparse — only unread Messages have GSI1 attributes).
- **Mark Message as Read**: UpdateItem that sets `Unread = False` AND REMOVEs `GSI1PK`/`GSI1SK` (drops the item out of the sparse index).
- **Send Hot New Deal Message to all Users**: Scan the `UserIndex` sparse GSI (only Users projected); for each user, create a Message.
- **Send new Brand Deal Message to Brand Watchers**: triggered by DynamoDB Stream on Deal insert — Query `BRANDWATCH#<Brand>` partition, fan out Messages.
- **Send new Category Deal Message to Category Watchers**: same pattern against `CATEGORYWATCH#<Category>` partition.

## Table Design
Composite primary key (`PK`, `SK`). FOUR secondary indexes: `GSI1` (deals by overall timestamp bucket + unread messages), `GSI2` (deals by brand+timestamp bucket), `GSI3` (deals by category+timestamp bucket), `UserIndex` (sparse — only User items).

| PK | SK | Type | Notes |
|---|---|---|---|
| `DEAL#1V...` | `DEAL#1V...` | Deal | Has GSI1/GSI2/GSI3 attrs |
| `BRAND#APPLE` | `BRAND#APPLE` | Brand | `LikesCount`, `WatchCount` |
| `BRANDS` | `BRANDS` | Brands singleton | `Brands` (set attr of all brand names) |
| `BRANDLIKE#APPLE#alexdebrie` | `BRANDLIKE#APPLE#alexdebrie` | BrandLike | Uniqueness guard |
| `BRANDWATCH#APPLE` | `USER#alexdebrie` | BrandWatch | All watchers share a partition |
| `CATEGORY#TECH` | `CATEGORY#TECH` | Category | `FeaturedDeals` complex attr |
| `FRONTPAGE` | `FRONTPAGE` | Singleton | Featured deals embedded |
| `EDITORSCHOICE` | `EDITORSCHOICE` | Singleton | Featured deals embedded |
| `USER#alexdebrie` | `USER#alexdebrie` | User | Has `UserIndex` attr |
| `MESSAGES#alexdebrie` | `MESSAGE#<KSUID>` | Message | GSI1 attrs only if unread |
| `DEALSCACHE#3` | `DEALSCACHE#3` | (optional) shard | Cached latest-deals payload |

## Key Techniques Used
- **Time-bucket sharding (Ch 13/15)**: latest-deals queries use `DEALS#<TruncatedTimestamp>` (e.g. truncated to the day) as the partition key. Bounds partition size AND lets app code "walk backward" day-by-day with a max-attempts guard if a single bucket is short.
- **Read-shard cache items (Ch 17)**: optional `DEALSCACHE#<N>` items duplicate the same payload across N partitions; reads pick a random shard to avoid hot-key throttling on the front page. Refreshed by a DynamoDB-Streams Lambda when a new Deal lands.
- **Singleton items (Ch 16)**: `BRANDS`, `FRONTPAGE`, `EDITORSCHOICE` use fixed PK/SK values rather than parameterized keys; they hold curated lists denormalized as complex attributes.
- **Sparse index for entity-type filtering (Ch 13)**: `UserIndex` is populated only by User items (only Users have the `UserIndex` attribute), so a Scan over it returns ALL users without touching any other entity. Enables the "blast all users" workflow.
- **Sparse index for status filtering within an entity (Ch 13)**: `GSI1` for Messages contains ONLY unread messages — marking-as-read REMOVEs the GSI1 attributes, automatically pulling the row out of the index.
- **Tracking item + counter via transaction (Ch 16)**: Like/Watch creates a uniqueness-enforced tracking item AND increments a counter on the parent, atomically.
- **Watchers share a partition deliberately**: `BRANDWATCH#<Brand>` PK with `USER#<Username>` SK enables a single Query to enumerate all watchers for notification fanout.
- **Denormalize curated lists, NOT user-driven lists**: Featured deals (editor-curated, small N) go inline on the Category/Page items; Likes/Watches (user-driven, unbounded) get separate items.
- **DynamoDB Streams for reactive fanout (Ch 17)**: new-Deal events trigger notification messages to brand/category watchers without touching the hot write path.

## Notable Code Examples
```python
def get_messages_for_user(username, unread_only=False):
    args = {
        'TableName': 'BigTimeDeals',
        'KeyConditionExpression': '#pk = :pk',
        'ExpressionAttributeNames': {'#pk': 'PK'},
        'ExpressionAttributeValues': {':pk': {'S': f"MESSAGE#{username}"}},
        'ScanIndexForward': False,
    }
    if unread_only:
        args['IndexName'] = 'GSI1'
    return client.query(**args)
```
- **What it demonstrates**: a single function services both "all messages" and "unread only" by swapping the index. The sparse-index design means no FilterExpression, no overfetch, no client-side filtering — the index itself encodes the filter.

```python
def mark_message_read(message):
    client.update_item(
        TableName='BigTimeDeals',
        Key={'PK': {'S': f"MESSAGE#{message.username}"},
             'SK': {'S': f"MESSAGE#{message.created_at}"}},
        UpdateExpression="SET #unread = :false REMOVE #gsi1pk, #gsi1sk",
        ExpressionAttributeNames={'#unread': 'Unread',
                                   '#gsi1pk': 'GSI1PK', '#gsi1sk': 'GSI1SK'},
        ExpressionAttributeValues={':false': {'S': 'False'}},
    )
```
- **What it demonstrates**: REMOVE on indexed attributes is how you EVICT a row from a sparse GSI — the row stays in the base table; it just disappears from the index.

## Anti-patterns Avoided
- **Don't put all "latest" items in one partition.** A naive `LATESTDEALS` PK would create both a fat partition (storage) and a hot partition (read traffic). Time-bucketing solves storage; cache shards solve traffic.
- **Don't use scatter-gather random sharding for "fetch latest" reads.** Reading N random partitions and re-merging is operationally painful and slower than a single time-bucket Query.
- **Don't store all usernames in a singleton.** Users are unbounded; the 400 KB cap will be hit. Use a sparse-index Scan over a User-only GSI instead.
- **Don't put all Users in a static-PK secondary index for grouping.** Every user write would slam one partition — write hot key. Sparse index with Scan distributes writes across many partitions while still allowing enumeration.
- **Don't use a FilterExpression for the unread-messages access pattern.** Filter expressions still consume RCUs for filtered-out items. Sparse index gives you exactly the rows you want.
- **Don't pre-join Brand items with Deal items in the same item collection.** That would force you to abandon the time-bucket sharding for deals. Better to issue Brand-GetItem and latest-deals Query IN PARALLEL.
- **Don't try to use a single complex attribute for likers/watchers.** They're unbounded — 400 KB will be hit. Tracking items + counters scale indefinitely.

## Lessons For Your Own Models
1. **"Fetch latest X" partitioned by a truncated timestamp** is the standard answer when X is high-volume and globally ordered. Pick the bucket granularity to keep partitions in the low-thousands of items.
2. **Cache hot reads INSIDE DynamoDB by duplicating a payload across N shard items.** No Redis required — the read path just picks a random shard. Refresh via Streams + Lambda on writes.
3. **Curated lists (editor-set, small N) belong DENORMALIZED on a parent or singleton item.** User-generated lists (unbounded N) belong as separate items.
4. **Two distinct sparse-index strategies exist** — (a) project a single entity TYPE for enumeration/scan, (b) project a single STATUS within an entity for filtering. Both are sparse but solve different problems.
5. **Watchers / subscribers should share a partition keyed by what's being watched.** Then notification fanout is one Query.
6. **Use DynamoDB Streams + Lambda for reactive fanout work** instead of doing it synchronously on the write path. The user's write returns fast; the fanout happens out-of-band.
7. **It is normal and good to use 3-4+ GSIs for a complex domain.** GSIs are how you make the same data accessible by different dimensions; don't ration them.
8. **Singleton items are the right tool for small, slowly-changing global state** (curated lists, configuration, the set of all brands). They're trivially cacheable and copyable across read shards.

## Connects To
- **Ch 11**: one-to-many — denormalized (Featured Deals on Category/Page) vs. parent-in-partition (Messages by user) vs. secondary-index regrouping (Deals by brand/category time bucket).
- **Ch 12**: multiple overloaded GSIs serving distinct access patterns.
- **Ch 13**: BOTH sparse-index strategies — entity-type filtering (UserIndex) and status filtering (unread Messages GSI1).
- **Ch 14**: KSUIDs for `DealId` and `MessageId` (sortable + unique).
- **Ch 15**: partition-size strategies and time-bucket sharding.
- **Ch 16**: singleton items; multi-attribute uniqueness; tracking items with counters.
- **Ch 17**: DynamoDB Streams for reactive fanout and cache refresh.
- **Ch 3**: TransactWriteItems for atomic like/watch + counter increment.
