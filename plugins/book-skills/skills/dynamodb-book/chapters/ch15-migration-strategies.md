# Chapter 15: Strategies for Migrations

## Core Idea
"What about migrations?" is the most common objection to single-table design. DeBrie's answer: migrations in DynamoDB are entirely manageable once you classify them. The decisive question is whether the change is **purely additive** (new attributes/items written going forward, existing items untouched) or whether it **requires editing existing items** (a Scan-driven ETL job). And when judging "additive," only *indexing attributes* count — application attributes are schemaless and always free to add.

## Frameworks Introduced

- **Adding new attributes to an existing entity**: Schemaless add, no ETL.
  - When to use: new application attribute (`Birthdate`, `FaxNumber`) that isn't part of any primary key or GSI.
  - How: just start writing the attribute. Handle absence at the data-mapping boundary of your app code with `.get('Attr', default)`. Works for denormalized one-to-many relationships too, since they're stored as attributes on the parent.

- **Adding a new entity type without relations**: Write into a new item collection; no ETL.
  - When to use: new entity has no "fetch parent + children" access pattern with existing entities.
  - How: define a new partition key pattern for the new entity (e.g. `ORG#<name>` + `PROJECT#<id>`) and start writing. Purely additive.

- **Adding a new entity type into an existing item collection**: Reuse an existing partition for the relational access pattern.
  - When to use: you have a new "fetch parent + children" access pattern AND the parent's existing item collection is currently unused (only contains the parent).
  - How: design the new child's sort key (e.g. `LIKE#<username>`) to colocate with the parent's PK (`POST#<PostId>`). Still purely additive — no existing items are touched.

- **Adding a new entity type into a new item collection**: Backfill GSI keys on existing parent items, then add new children.
  - When to use: new "fetch parent + children" access pattern AND the parent's existing item collection is already in use.
  - How: add `GSI1PK`/`GSI1SK` to the new child type at write time. Backfill the same attributes onto existing parent items via a Scan + UpdateItem ETL job. This is the first scenario that requires real migration work.

- **Joining existing items into a new item collection**: Reshape relationships among items that already exist.
  - When to use: no new entity type — you just need a new filter, sort order, or to colocate two existing types.
  - How: same as #4. Identify the items, define a new GSI item collection, run a Scan-driven UpdateItem job to add the GSI attributes.

- **Using parallel scans**: Speed up ETL jobs by partitioning the Scan across workers.
  - When to use: any non-trivial migration backfill — Scan throughput is the bottleneck.
  - How: pass `TotalSegments` (e.g. 10) and `Segment` (0..N-1) to each worker. DynamoDB handles state coordination; each segment is independent. Cost implication: each segment still consumes RCUs across the entire table partition slice, so a 10-segment scan reads the whole table 1x (not 10x) but in parallel — provision capacity or use on-demand to absorb the burst.

## Key Concepts
- **Additive vs. mutating** is the central classification.
- **Indexing attributes vs. application attributes** (from Ch 9) determines whether an "add" is truly free.
- The ETL pattern is always the same shape: `Scan` (filtered) → for each item, `UpdateItem` adding new attributes → loop on `LastEvaluatedKey`.
- DynamoDB transforms in/out of the database belong at the *boundary* of your application, so defaults for missing attributes live in one place.

## Mental Models
- A migration is "free" if and only if no existing item needs a new value for an indexed attribute.
- A new "fetch parent + children" access pattern is the most expensive migration class — it forces backfill onto parents.
- Parallel Scan is a knob, not a feature you build yourself.

## Anti-patterns
- Treating migrations as scary/unique — they're a finite set of five situations.
- Putting database-shape logic (default values for new attributes) sprinkled across business code instead of at the data-access boundary.
- Running a serial Scan for a large backfill when parallel segments cost the same and finish in a fraction of the time.
- Updating existing items one-at-a-time when `BatchWriteItem` (with proper error handling) would work.

## Code Examples

```python
# ETL backfill: add GSI1PK/GSI1SK to existing Post items
last_evaluated = None
params = {
    "TableName": "SocialNetwork",
    "FilterExpression": "#type = :type",
    "ExpressionAttributeNames": {"#type": "Type"},
    "ExpressionAttributeValues": {":type": {"S": "Post"}},
}

while True:
    if last_evaluated:
        params["ExclusiveStartKey"] = last_evaluated
    results = client.scan(**params)
    for item in results["Items"]:
        client.update_item(
            TableName="SocialNetwork",
            Key={"PK": item["PK"], "SK": item["SK"]},
            UpdateExpression="SET #gsi1pk = :gsi1pk, #gsi1sk = :gsi1sk",
            ExpressionAttributeNames={"#gsi1pk": "GSI1PK", "#gsi1sk": "GSI1SK"},
            ExpressionAttributeValues={
                ":gsi1pk": item["PK"],
                ":gsi1sk": item["SK"],
            },
        )
    if "LastEvaluatedKey" not in results:
        break
    last_evaluated = results["LastEvaluatedKey"]
```
- **What it demonstrates**: the canonical Scan-filter-UpdateItem loop. Production version layers in parallel segments, BatchWriteItem, retries, and progress checkpointing.

```python
# Parallel Scan parameters for a 10-worker ETL
params = {
    "TableName": "SocialNetwork",
    "FilterExpression": "#type = :type",
    "ExpressionAttributeNames": {"#type": "Type"},
    "ExpressionAttributeValues": {":type": "Post"},
    "TotalSegments": 10,
    "Segment": 0,   # worker 0 of 10
}
```
- **What it demonstrates**: DynamoDB handles all coordination — each worker just declares its segment number.

## Reference Tables

| Situation | Effort | Strategy |
|---|---|---|
| New attribute on existing entity | None | Add lazily in app code |
| New entity, no relations | None | New item collection |
| New entity, relation, parent collection unused | None | Place children in parent's collection |
| New entity, relation, parent collection in use | Backfill | Add GSI keys to parents via Scan ETL |
| New access pattern on existing items | Backfill | Add GSI keys via Scan ETL |
| Any backfill | n/a | Use parallel Scan |

## Key Takeaways
- The schemaless nature of DynamoDB makes most adds free.
- Only changes that require new *indexing* attributes on existing items need ETL.
- ETL is a templatable job — Scan, filter, update, paginate — not a bespoke project.
- Parallel Scan is the default for any non-trivial backfill.

## Connects To
- Chapter 9 (indexing attributes vs. application attributes; application/DB boundary)
- Chapter 11 (one-to-many strategies — denormalization makes attribute migrations free)
- Chapter 14 (sorting on changing attributes — same GSI-add pattern)
- Chapters 19, 22 (GitHub Migration worked example applies every strategy in this chapter)
