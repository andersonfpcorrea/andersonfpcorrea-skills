# Chapter 18: Building a Session Store

## Core Idea
First end-to-end worked example: a session-token store for an authenticated app. It is the ONLY example in the book that uses a simple (non-composite) primary key, chosen because there is effectively a single entity (Session) accessed by a unique token.

## Domain & Entities
You are issuing session tokens to authenticated users of a social-network-style app. Tokens expire 7 days after creation; users (or admins) can manually revoke ALL outstanding tokens for an account. Entities in the ERD are `Session` (the core entity) and `User`, but User is never persisted in this table — it appears only as a grouping attribute, so only `Session` is actually modeled.

## Access Patterns
- **Create Session**: insert a Session with a fresh UUID token. Primary-table PutItem with `attribute_not_exists(SessionToken)` to guarantee uniqueness.
- **Get Session**: look up a Session by its token. Primary-table GetItem on `SessionToken`, with a FilterExpression that drops items whose TTL has passed.
- **Delete Session (time-based)**: automatic expiry 7 days after creation. Handled entirely by DynamoDB TTL — no application code.
- **Delete Sessions for User (manual revocation)**: invalidate every session for a user. Query the `UserIndex` GSI by `Username` to enumerate tokens, then DeleteItem on each.

## Table Design
Simple primary key on the main table (no PK/SK overloading). Attribute names are meaningful (`SessionToken`, `Username`) rather than generic (`PK`, `SK`) because only one entity type lives in the table. One GSI (`UserIndex`) with a simple key schema partitioned by `Username` and a `KEYS_ONLY` projection. The `TTL` attribute is configured as the table's TTL field.

| SessionToken (PK) | Username | CreatedAt | ExpiresAt | TTL (epoch) |
|---|---|---|---|---|
| `0bc6bdf8-6dac-4212-b11a-81f784297c78` | `alexdebrie` | `2020-02-14T10:00:00` | `2020-02-21T10:00:00` | `1582279200` |
| `9f3e...` | `alexdebrie` | `2020-02-15T09:00:00` | `2020-02-22T09:00:00` | `1582362000` |
| `c2a4...` | `bountyhunter1` | `2020-02-16T11:00:00` | `2020-02-23T11:00:00` | `1582452000` |

GSI `UserIndex` (KEYS_ONLY):

| Username (PK) | SessionToken |
|---|---|
| `alexdebrie` | `0bc6bdf8-...` |
| `alexdebrie` | `9f3e...` |
| `bountyhunter1` | `c2a4...` |

## Key Techniques Used
- **DynamoDB TTL (Ch 3)**: offloads time-based deletion to AWS — no cron, no Lambda sweeper. Stored as a duplicate epoch attribute alongside the human-readable ISO8601 `ExpiresAt`.
- **Condition expression for uniqueness (Ch 6)**: `attribute_not_exists(SessionToken)` prevents accidental token collision when two PutItems race on the same UUID.
- **Filter expression as TTL safety net (Ch 6)**: AWS only guarantees TTL deletion within 48 hours, so reads layer a `FilterExpression` that hides items whose TTL is in the past.
- **Secondary index for inverse lookup (Ch 12)**: `UserIndex` lets you go from a Username back to all of that user's session tokens, which the base table's key schema can't do.
- **KEYS_ONLY projection (Ch 12)**: minimizes GSI storage and write cost — the manual-revocation flow only needs the primary key to issue DeleteItems.

## Notable Code Examples
```python
epoch_seconds = int(time.time())
result = dynamodb.query(
    TableName='SessionStore',
    KeyConditionExpression="#token = :token",
    FilterExpression="#ttl <= :epoch",
    ExpressionAttributeNames={"#token": "SessionToken", "#ttl": "TTL"},
    ExpressionAttributeValues={
        ":token": {"S": "0bc6bdf8-..."},
        ":epoch": {"N": str(epoch_seconds)},
    },
)
```
- **What it demonstrates**: combining KeyConditionExpression with a FilterExpression to defend against the TTL deletion lag — even if DynamoDB hasn't reaped the row yet, an expired token never reaches your app.

## Anti-patterns Avoided
- **Don't roll your own expiration sweeper.** A pro-active scan-and-delete background job adds infrastructure cost, complexity, and another partition to maintain. TTL is free.
- **Don't rely purely on lazy expiration.** Lazy deletion (delete on read) leaves dead tokens accumulating in the table forever for users who never come back, wasting storage.
- **Don't trust TTL alone for security boundaries.** TTL deletion can lag up to 48 hours; always layer a FilterExpression so an expired token is never honored.
- **Don't store the user's session token under a `User`-keyed primary key.** That would let two users share a token if a collision ever occurred — uniqueness must be enforced in the PK.

## Lessons For Your Own Models
1. **A simple primary key is legitimate when you truly have one entity and only key-based access patterns.** Don't reach for composite keys + `PK`/`SK` overloading reflexively.
2. **Uniqueness on a value means that value must BE (part of) the partition key.** It can't be enforced from a GSI.
3. **Duplicate timestamps when formats serve different purposes** — ISO8601 for humans and debugging, epoch integer for TTL. Storage is cheap; legibility isn't.
4. **TTL is your default deletion mechanism for any time-bounded entity** (sessions, password reset tokens, ephemeral OTPs, signed URLs).
5. **Use KEYS_ONLY GSIs for "lookup then act" flows.** When the GSI exists only to find primary keys for follow-up writes, projecting more attributes is waste.

## Connects To
- **Ch 3**: TTL configuration and the 48-hour lag caveat.
- **Ch 6**: condition expressions for uniqueness; filter expressions as TTL backstop.
- **Ch 12**: secondary index design and projection types.
- **Ch 16**: uniqueness-on-an-attribute strategies (single-attribute case here; multi-attribute version appears in Ch 19).
