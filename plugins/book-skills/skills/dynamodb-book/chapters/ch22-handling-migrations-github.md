# Chapter 22: Handling Migrations in our GitHub example

## Core Idea
Take the Ch 21 GitHub model into production, then a year later add new entities and refactor a bad decision. Proves the common worry about DynamoDB — "migrations are scary" — is overblown: with planning, a single-table model is migratable using a small set of repeatable strategies.

## The Four Changes
DeBrie ranks migration difficulty (easiest to hardest) and tackles them in that order:

1. **Adding a Code of Conduct** — new one-to-one attribute on Repo (basically adding an attribute).
2. **Adding GitHub Gists** — new entity, new one-to-many relationship with User, fits in an *existing* item collection.
3. **Adding GitHub Apps** — new entity with a one-to-many relationship (Account → Apps) AND a many-to-many relationship (Apps ↔ Repos). Requires decorating existing items with new GSI keys.
4. **Refactoring Open/Closed Issue/PR access** — replace the Ch 21 filter expression with a primary-key encoding. The hardest migration: redesign + ETL + two new GSIs.

The ranking generalizes:
1. New attribute
2. New entity, no relationship
3. New entity + relationship into an existing item collection
4. New entity + relationship into a new item collection
5. Migrating existing patterns

## Change 1: Code of Conduct (one-to-one)
- A Code of Conduct has a `name` and `url`. Tiny, always fetched with the Repo.
- Stored as a `CodeOfConduct` map attribute directly on the Repo item.
- **Migration strategy: lazy.** Not used for indexing. Not required. No ETL. As users register a Code of Conduct, the app writes the attribute. Existing Repos without one are simply missing the attribute.

## Change 2: Gists (one-to-many with User)
- Access pattern: "Fetch User and Gists for User" — need Gists in the same item collection as the User.
- The User's main-table item collection in Ch 21 was *empty* (User was a lone item). Free real estate.
- New Gist item:
  - `PK: ACCOUNT#<username>`
  - `SK: #GIST#<KSUID>` — the `#` prefix sorts Gists *before* the User item so `ScanIndexForward=False` Query returns User + most-recent Gists.
- KSUIDs give time-ordered IDs without coordination.
- **Migration strategy: no ETL.** New entity, no existing items touched. Reusing an empty existing item collection is the cleanest possible new-entity migration.

```python
dynamodb.query(
    TableName='GitHubTable',
    KeyConditionExpression="#pk = :pk",
    ExpressionAttributeNames={"#pk": "PK"},
    ExpressionAttributeValues={":pk": {"S": "ACCOUNT#alexdebrie"}},
    ScanIndexForward=False
)
```

## Change 3: GitHub Apps (one-to-many + many-to-many)

**One-to-many (Account → Apps)** — can't fit in the main-table Account collection because the scan direction differs between User collections (Gists before User) and Org collections (Memberships after Org). Solved in GSI1 (Account items had nothing in GSI1 yet):

- **GitHubApp**: `PK: APP#<account>#<app>`, `SK: APP#<account>#<app>`, `GSI1PK: ACCOUNT#<account>`, `GSI1SK: APP#<appName>`
- **User/Org**: add `GSI1PK: ACCOUNT#<account>`, `GSI1SK: ACCOUNT#<account>`

This is the first migration that requires touching existing items — User and Org items need `GSI1PK`/`GSI1SK` added.

**Migration strategy: Scan + UpdateItem ETL.** DeBrie shows the actual script:

```python
last_evaluated = ''
while True:
    params = {
        "TableName": "GithubModel",
        "FilterExpression": "#type IN (:user, :org)",
        "ExpressionAttributeNames": {"#type": "Type"},
        "ExpressionAttributeValues": {":user": {"S": "User"}, ":org": {"S": "Organization"}}
    }
    if last_evaluated:
        params['ExclusiveStartKey'] = last_evaluated
    results = client.scan(**params)
    for item in results['Items']:
        client.update_item(
            TableName='GitHubModel',
            Key={'PK': item['PK'], 'SK': item['SK']},
            UpdateExpression="SET #gsi1pk = :gsi1pk, #gsi1sk = :gsi1sk",
            ExpressionAttributeNames={'#gsi1pk': 'GSI1PK', '#gsi1sk': 'GSI1SK'},
            ExpressionAttributeValues={':gsi1pk': item['PK'], ':gsi1sk': item['SK']}
        )
    if not results['LastEvaluatedKey']:
        break
    last_evaluated = results['LastEvaluatedKey']
```

He notes you'd add parallel scans, error handling, and BatchWriteItem for production. The alternative is the lazy approach: only add GSI keys to Accounts when they create their first App.

**Many-to-many (Apps ↔ Repos)** — adjacency list pattern. New `AppInstallation` item lives in two item collections:

- **AppInstallation**: `PK: APP#<acct>#<app>`, `SK: REPO#<owner>#<repo>`, `GSI1PK: REPO#<owner>#<repo>`, `GSI1SK: REPOAPP#<appOwner>#<appName>`

Lands in App's main-table collection (which had nothing in it yet) for "Get App + installations", and lands in Repo's GSI1 collection for "Get Repo + installed apps". Zero changes to existing Repo or App items beyond the AppInstallation writes themselves.

## Change 4: Refactor Open/Closed Issue/PR access
Ch 21 used a filter expression on `Status`. Production exposed two failures:
1. **Long-stale repos**: most recent items are mostly Open, so finding 25 Closed items required scanning deep into the repo.
2. **Over-fetching thrash**: app fetched 75 items per page hoping to filter down to 25; sometimes too many, sometimes not enough.

Fix: build the status into the sort key. Each access pattern is a double filter (match Status AND sort by issue number) — a composite sort key job (Ch 13).

First attempt: `SK = ISSUE#<STATUS>#<number>`. Lexicographic ordering gives `CLOSED < OPEN < REPO`, so fetching "Repo + most recent Closed" can't be done with one Query.

Second attempt: prefix Repo and Closed with `#`. Now `#REPO < #ISSUE#CLOSED < ISSUE#OPEN`. Repo sits between them. But Open Issues are now ascending when fetched forward from Repo — wrong order.

Final solution — **inverted-number trick**: store Open Issues with sort key `ISSUE#OPEN#<99999999 - issueNumber>`. Issue #15 becomes `ISSUE#OPEN#99999984`. Now scanning *forward* from the Repo item returns Open issues in descending issue-number order. One GSI handles both Open and Closed for Issues. Same trick for PRs.

Final layout (new indexes GSI4 for Issues, GSI5 for PRs):

| Entity | GSI4PK | GSI4SK |
|---|---|---|
| Repo | `REPO#<owner>#<repo>` | `#REPO#<owner>#<repo>` |
| OpenIssue | `REPO#<owner>#<repo>` | `ISSUE#OPEN#<zeroPaddedDiff>` |
| ClosedIssue | `REPO#<owner>#<repo>` | `#ISSUE#CLOSED#<zeroPaddedNumber>` |

**Migration strategy: ETL all existing Issue/PR items** to add GSI4/GSI5 attributes, then create the indexes. Same Scan + UpdateItem pattern as Change 3.

DeBrie's caveat: "this is one of the weirder patterns in DynamoDB... indecipherable outside the context of your application's direct data access needs." He explicitly offers the escape hatch: pay for 4 separate GSIs (Repo+OpenIssues, Repo+ClosedIssues, Repo+OpenPRs, Repo+ClosedPRs) if the inverted-number math makes you uncomfortable.

## Key Migration Techniques
- **Lazy migration**: for non-indexed attributes, just write on next update. No ETL.
- **Reuse empty item collections**: a User collection with only a User item is a free home for a new child entity.
- **Decorate existing items via Scan + UpdateItem**: when a new GSI needs keys on existing items. Mention parallel scans + BatchWriteItem for scale.
- **Adjacency list (Ch 12)**: many-to-many via a join item that participates in both sides' collections — base table for one side, GSI for the other.
- **Composite sort key with inverted numeric (Ch 13, Ch 14)**: encode a status filter + reverse sort in a single SK by using `MAX - n` instead of `n`.

## Anti-patterns Avoided / Tradeoffs Acknowledged
- **Don't over-engineer migrations upfront** — Code of Conduct needs zero ETL because it's just a Repo attribute. Don't migrate what doesn't need migrating.
- **Filter expressions DO sometimes fail in production** — Ch 21 accepted one as a tradeoff; Ch 22 honestly shows the failure mode and reverses the call.
- **Inverted-number trick is admittedly ugly** — DeBrie names it as "weird" and "machine code," and offers the more-readable alternative (more GSIs). Naming the smell is part of the technique.
- **Two new GSIs (GSI4, GSI5) on the same table** — the model now has 5 GSIs. Single-table doesn't mean single-index, and adding indexes during migration is normal.

## Lessons For Your Own Models
1. **Migrations have a difficulty ladder** — start with the easiest (new attribute) and work up to the hardest (refactoring existing access patterns). Most migrations are at the easy end.
2. **Lazy migration is real**: if an attribute isn't indexed and isn't required, don't write an ETL — let writes backfill organically.
3. **Empty item collections are migration goldmines** — check existing collections before creating new ones.
4. **The Scan + UpdateItem script is the standard ETL pattern** — keep a parallel-scan, batch-write version in your toolbox. You'll run variants of it for every non-trivial migration.
5. **Production will reveal bad bets** — DeBrie publicly walks back the Ch 21 filter-expression decision. Build observability so you can detect over-fetching, then iterate.
6. **When a pattern feels weird, name it and offer an escape hatch** — clever encodings (inverted numbers) are valid but should be documented as "we did this to save N GSIs at the cost of readability."
7. **Adding GSIs during migration is expected** — don't treat GSI count as a virtue. Treat each one as paying for specific access patterns.

## Connects To
- **Ch 12**: shallow duplication and adjacency list — both reappear here.
- **Ch 13**: composite sort key for the Open/Closed refactor; filter-expression caveats validated by production.
- **Ch 14**: KSUIDs for Gists, zero-padding for the inverted-number trick.
- **Ch 15**: migration strategy catalog — this chapter is the worked application of that chapter.
- **Ch 21**: the model being migrated. Read together as one continuous example.
