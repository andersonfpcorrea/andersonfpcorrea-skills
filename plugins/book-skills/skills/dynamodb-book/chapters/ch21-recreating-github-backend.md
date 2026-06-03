# Chapter 21: Recreating GitHub's Backend

## Core Idea
The book's masterclass example: re-create GitHub's core metadata API (Repos, Users, Orgs, Issues, PRs, Comments, Reactions, Stars, Forks) in a single DynamoDB table with 24 access patterns. Proves that even highly relational, multi-entity domains can be served from one table when modeled deliberately.

## Domain & Entities
- **Repo** — owned by a User or Organization, identified by `<owner>/<name>`. Owners and repo names form a unique pair.
- **User** — individual GitHub account.
- **Organization** — shared account; competes with Users in the same name namespace (no User and Org may share a name).
- **Membership** — tracks a User belonging to an Organization (many-to-many).
- **Payment Plan** — one-to-one with User or Organization; stored inline on the parent.
- **Issue** — child of Repo; shares an auto-incrementing number sequence with PRs in the same repo.
- **Pull Request** — child of Repo; shares the number sequence with Issues.
- **Comment** (IssueComment / PRComment) — children of Issue or PR.
- **Reaction** — applied to an Issue, PR, IssueComment, or PRComment; eight reaction types.
- **Star** — User stars a Repo (many-to-many).
- **Fork** — a forked Repo IS itself a Repo, with a pointer back to the upstream.

## Access Patterns
24 patterns total, grouped by DeBrie:

**Repo basics:**
- **Get / Create Repo** → main PK `REPO#<owner>#<name>`
- **Get / Create / List Issue(s) for Repo** → main table Query, same item collection as Repo
- **Get / Create / List Pull Request(s) for Repo** → GSI1 Query (Repo+PRs in same collection)
- **Fork Repo** → main table Put (a Fork is just a Repo)
- **Get Forks for Repo** → GSI2 Query on `REPO#<originalOwner>#<name>`

**Interactions:**
- **Add Comment to Issue** → main table Put with `ISSUECOMMENT#...` PK
- **Add Comment to Pull Request** → main table Put with `PRCOMMENT#...` PK
- **Add Reaction to Issue / Pull Request / Comment** → TransactWriteItems (Reaction item + counter update)
- **Star Repo** → TransactWriteItems (Star item + `StarCount` increment)
- **Get Stargazers for Repo** → main table Query, same collection as Repo (Star items sort after Repo)

**User management:**
- **Create User** → main table Put `ACCOUNT#<username>`
- **Create Organization** → main table Put `ACCOUNT#<orgname>` (same key shape as User — guarantees namespace uniqueness)
- **Add User to Organization** → write Membership item + update embedded `Organizations` map on User
- **Get Users for Organization** → main table Query on Org's collection (Membership items)
- **Get Organizations for User** → read embedded `Organizations` map from User item (shallow duplication)

**Accounts & Repos:**
- **Get Repos for User** → GSI3 Query on `ACCOUNT#<username>`
- **Get Repos for Organization** → GSI3 Query on `ACCOUNT#<orgname>`

Plus the implicit per-entity fetches (Get Issue, Get PR, Get Comment, Get Reaction) served by direct GetItem.

## Table Design
Main table uses a composite primary key (`PK`, `SK`). Three GSIs, each overloaded:

- **GSI1** — co-locates Repos with their Pull Requests (couldn't fit in main table because both Issues and PRs want descending-by-number order in the same collection).
- **GSI2** — co-locates a Repo with all Forks of that Repo (forks key on the *original* owner).
- **GSI3** — co-locates an Account (User or Org) with all Repos it owns, sorted by `UpdatedAt`.

Attribute naming convention: `PK`/`SK` for the main table, `GSI1PK`/`GSI1SK`, `GSI2PK`/`GSI2SK`, `GSI3PK`/`GSI3SK`.

Example items in the main table:

| PK | SK | Type | Notable attrs |
|---|---|---|---|
| `REPO#alexdebrie#dynamodb-book` | `ISSUE#0000003` | Issue | `Status=Open` |
| `REPO#alexdebrie#dynamodb-book` | `ISSUE#0000004` | Issue | `Status=Closed` |
| `REPO#alexdebrie#dynamodb-book` | `REPO#alexdebrie#dynamodb-book` | Repo | `StarCount`, `ForkCount`, `IssuesAndPullRequestCount`, `CodeOfConduct`, `GSI1PK`, `GSI2PK`, `GSI3PK` |
| `REPO#alexdebrie#dynamodb-book` | `STAR#danny-developer` | Star | |
| `PR#alexdebrie#dynamodb-book#0000001` | `PR#alexdebrie#dynamodb-book#0000001` | Pull Request | `GSI1PK=REPO#alexdebrie#dynamodb-book`, `GSI1SK=PR#0000001` |
| `ISSUECOMMENT#alexdebrie#dynamodb-book#3` | `ISSUECOMMENT#<ksuid>` | IssueComment | |
| `ISSUEREACTION#alexdebrie#dynamodb-book#4#happy-harry` | same | Reaction | `Reactions` string set |
| `ACCOUNT#alexdebrie` | `ACCOUNT#alexdebrie` | User | `Organizations` map, `PaymentPlan` map, `Type=User` |
| `ACCOUNT#megacorp` | `ACCOUNT#megacorp` | Organization | `PaymentPlan`, `Type=Organization` |
| `ACCOUNT#megacorp` | `MEMBERSHIP#alexdebrie` | Membership | `Role=Admin` |

The Repo item sits in the *middle* of its main-table item collection: Issues sort *before* it (descending by number gives most-recent-first), Stars sort *after* it.

## Key Techniques Used
- **Composite primary key (Ch 2)**: required for the multi-relationship modeling.
- **Item collection with parent in the middle (Ch 14)**: Repo sits between Issues (sort before) and Stars (sort after) in the same collection — one Query serves two one-to-many relationships.
- **GSI overloading (Ch 3, Ch 11)**: every GSI serves multiple entity types via key-prefix encoding.
- **Zero-padded numeric sort keys (Ch 14)**: 7-digit issue numbers so string-lexicographic sort matches numeric sort.
- **Shared namespace via shared key shape**: User and Organization both use `ACCOUNT#<name>` — DynamoDB's uniqueness constraint then enforces the cross-entity name rule.
- **Shallow duplication (Ch 12)**: User stores embedded `Organizations` map (bounded ~40) so "Get Organizations for User" is a single GetItem; the reverse direction uses Membership items because Org membership is unbounded.
- **Reference counts via DynamoDB Transactions (Ch 8)**: `StarCount`, `ForkCount`, `IssuesAndPullRequestCount`, and the eight reaction counters are updated atomically with the related write so counts never drift.
- **Auto-incrementing IDs**: emulated with UpdateItem (`ReturnValues=UPDATED_NEW`) on a counter attribute followed by PutItem with the returned value.
- **KSUIDs for comment IDs (Ch 14)**: time-sortable unique identifiers without coordination.
- **Filter expression accepted as a tradeoff**: Open/Closed filter on Issues uses a filter expression because there are only two values, items are small, and the page size is 25 (DeBrie later reverses this in Ch 22).

## Notable Code Examples

Auto-incrementing issue number — two requests, the second uses the value returned by the first:

```python
resp = client.update_item(
    TableName='GitHubTable',
    Key={'PK': {'S': 'REPO#alexdebrie#dynamodb-book'},
         'SK': {'S': 'REPO#alexdebrie#dynamodb-book'}},
    UpdateExpression="SET #count = #count + :incr",
    ExpressionAttributeNames={"#count": "IssuesAndPullRequestCount"},
    ExpressionAttributeValues={":incr": {"N": "1"}},
    ReturnValues='UPDATED_NEW'
)
current_count = resp['Attributes']['IssuesAndPullRequestCount']['N']

client.put_item(
    TableName='GitHubTable',
    Item={'PK': {'S': 'REPO#alexdebrie#dynamodb-book'},
          'SK': {'S': f"ISSUE#{current_count.zfill(7)}"},
          ...}
)
```
- **What it demonstrates**: emulating SQL's auto-increment without a sequence — two round trips, but correct under concurrency because UpdateItem is atomic.

Atomic star + counter using a transaction:

```python
dynamodb.transact_write_items(TransactItems=[
    {"Put": {"Item": {"PK": {"S": "REPO#alexdebrie#dynamodb-book"},
                       "SK": {"S": "STAR#danny-developer"}, ...},
             "ConditionExpression": "attribute_not_exists(PK)"}},
    {"Update": {"Key": {"PK": {"S": "REPO#alexdebrie#dynamodb-book"},
                        "SK": {"S": "#REPO#alexdebrie#dynamodb-book"}},
                "UpdateExpression": "SET #count = #count + :incr",
                "ExpressionAttributeNames": {"#count": "StarCount"},
                "ExpressionAttributeValues": {":incr": {"N": "1"}}}}
])
```
- **What it demonstrates**: "no duplicate star" and "increment counter" succeed or fail together — the counter never drifts from the underlying Star items.

## Anti-patterns Avoided / Tradeoffs Acknowledged
- **Couldn't fit Issues AND PRs in the Repo's main-table collection** — both want descending-by-number, so DeBrie pushed PRs to GSI1. He's explicit: when two relationships need the same sort direction, you need an extra index.
- **Accepted a filter expression for Open/Closed status** despite Ch 13's warning — judged acceptable because the cardinality is 2 and page size is small. Ch 22 walks back this decision after production showed it didn't work.
- **Auto-increment requires two round trips** — there's no DynamoDB primitive for it; he doesn't pretend otherwise.
- **Three GSIs** — single-table doesn't mean single-index. Each GSI was added only when no existing index could serve the pattern.
- **`Type` attribute is not in the PK** for distinguishing User vs Organization. He accepts this because no access pattern says "fetch all Users" or "fetch all Orgs" — only per-name lookups.

## Lessons For Your Own Models
1. **List every access pattern before keying** — DeBrie has 24 patterns mapped before the first PK is chosen. The key design is a *consequence* of the pattern list, not an upfront guess.
2. **One item collection can serve two one-to-many relationships** if one sorts ascending and the other descending — put the parent in the middle.
3. **Use shared key prefixes to enforce cross-entity uniqueness** — User and Org both keying on `ACCOUNT#<name>` is how the namespace constraint is enforced, for free.
4. **Add a GSI only when no existing collection fits** — DeBrie reuses item collections aggressively before reaching for a new index. Three GSIs for 24 patterns is the achievement.
5. **Reference counts belong in a transaction with the underlying write** — never recount, never let the counter drift.
6. **Shallow duplication is fine when one side is bounded** — embed the small side, query the unbounded side.

## Connects To
- **Ch 2**: composite primary key fundamentals.
- **Ch 3 / Ch 11**: GSI overloading — every GSI here serves 2+ entity types.
- **Ch 8**: transactions for atomic multi-item writes (stars, reactions, counters).
- **Ch 12**: many-to-many strategies (shallow duplication for User→Orgs, separate item for Org→Users).
- **Ch 13**: filter-expression tradeoffs (the Open/Closed decision he revisits in Ch 22).
- **Ch 14**: one-to-many strategies, zero-padding for lexicographic sort, KSUIDs, parent-in-the-middle pattern.
- **Ch 22**: takes this exact model and migrates it to add Codes of Conduct, Gists, GitHub Apps, and fix the Open/Closed decision.
