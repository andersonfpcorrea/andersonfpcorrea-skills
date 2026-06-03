# Chapter 9: From Modeling to Implementation

## Core Idea
90% of DynamoDB work happens before any code is written. Once the model is set, six implementation tactics keep the table maintainable, debuggable, and migratable: separate concerns at the boundary, never reuse indexing attributes, add a `Type` tag, write debug scripts, and (advanced) shorten attribute names.

## Frameworks Introduced
- **Boundary Implementation Pattern**: All DynamoDB-specific logic (key construction, type conversion, indexing attributes, attribute-name expansion) lives in a single data-access module at the edge of the application. The application core operates only on domain objects.
  - When to use: Every DynamoDB-backed app, regardless of size.
  - How: Build a `data` module with functions like `get_user(username)` and `save_user(user)`. These functions translate between domain objects and DynamoDB items. Nothing else in the app touches the DynamoDB client.

- **Attribute Separation Principle**: Distinguish "indexing attributes" (`PK`, `SK`, `GSI1PK`, `GSI1SK`) from "application attributes" (`Username`, `FirstName`). Both live on the item — never derive app attributes from index attributes.

## Key Concepts
- **Indexing attributes**: Attributes that exist only to power DynamoDB's primary key and secondary indexes. No business meaning.
- **Application attributes**: Attributes meaningful to business logic. Often used to construct indexing attributes, never the reverse.
- **Type attribute**: A string attribute on every item declaring its entity type (`"User"`, `"Order"`, `"SensorReading"`). Pays off in migrations, analytics, and console debugging.
- **Generic GSI naming**: `GSI1`, `GSI2` with `GSI<N>PK` / `GSI<N>SK` attributes — never give GSIs descriptive names.
- **Debug scripts**: Small CLI scripts (e.g., using `click`) wrapping data-access functions to exercise individual access patterns from the terminal.
- **Attribute name abbreviation**: Storing `u`, `fn`, `ln` instead of `Username`, `FirstName`, `LastName` to save storage. Safe because the application boundary translates names.

## Mental Models
- **Use the boundary pattern when** writing any DynamoDB code — never let raw DynamoDB items leak into business logic.
- **Use generic GSI names when** designing GSIs — descriptive names lock you into a single use case and break under overloading.
- **Use a Type attribute when** writing any item — even if you don't need it now, migrations and analytics will.
- **Use debug scripts when** implementing each access pattern — write them alongside the data layer, not after.
- **Use abbreviated attribute names when** storing billions+ of items where storage cost dominates; otherwise skip.

## Anti-patterns
- **Removing application attributes because they're encoded in `PK`**: Risks data loss on future indexing changes; saves negligible storage.
- **Reusing an attribute across multiple indexes** (e.g., using `SK` as both the base table's sort key AND `GSI1SK`): Looks like a storage win but creates conflicts when entity types diverge across indexes. Always allocate fresh `GSI<N>PK` / `GSI<N>SK` attributes.
- **Touching DynamoDB items in business logic**: Spreads type conversion, key construction, and DynamoDB-specific concerns across the app.
- **Skipping the Type attribute**: Makes ETL migrations and analytics exports painful.
- **Using the AWS console as a primary debug tool**: Indexed attributes and abbreviated names make manual exploration error-prone — write scripts instead.
- **Abbreviating attribute names for small/medium tables**: Marginal savings, real cognitive cost.

## Code Examples
```python
# Boundary pattern: get_user translates DynamoDB item -> domain object
def get_user(username):
    resp = client.get_item(
        TableName='AppTable',
        Key={'PK': {'S': f'USER#{username}'}}
    )
    return User(
        username=resp['Item']['Username']['S'],
        first_name=resp['Item']['FirstName']['S'],
        last_name=resp['Item']['LastName']['S'],
    )
```
- **What it demonstrates**: All DynamoDB-specific concerns (key construction, type unwrapping) confined to the data module.

```python
# Save with Type attribute and indexing attributes
def save_user(user: User):
    client.put_item(
        TableName='AppTable',
        Item={
            'PK': {'S': f'USER#{user.username}'},
            'SK': {'S': f'USER#{user.username}'},
            'GSI1PK': {'S': f'ORG#{user.org_name}'},
            'GSI1SK': {'S': f'USER#{user.username}'},
            'Type': {'S': 'User'},
            'Username': {'S': user.username},
            'FirstName': {'S': user.first_name},
            'LastName': {'S': user.last_name},
        }
    )
```
- **What it demonstrates**: Indexing attributes, Type tag, and application attributes coexisting at the boundary.

```python
# Debug script per access pattern
@click.command()
@click.option('--username')
def get_user(username):
    print(data.get_user(username))
```
- **What it demonstrates**: A CLI wrapper around the data layer for fast iteration on a single access pattern.

## Reference Tables

| Tactic | Cost | Benefit | Worth it? |
|---|---|---|---|
| Boundary pattern | Slightly more code in data module | Clean app core, testable, swappable | Always |
| Separate app from indexing attrs | Slightly larger items | Safe migrations, no data loss | Always |
| Type attribute on every item | One extra string per item | Easier ETL, analytics, console | Always |
| Don't reuse attrs across indexes | Marginal extra storage | Avoids modeling knots; flexibility | Always |
| Debug scripts per access pattern | Time to write | Faster development & debugging | Always |
| Shorten attribute names | Less readable raw items | Storage savings | Only at billions+ items |

| Layer | Touches DynamoDB? | Works with |
|---|---|---|
| Application core (handlers, services) | No | Domain objects (`User`, `Order`) |
| Data module (boundary) | Yes | DynamoDB items, keys, expressions |

## Key Takeaways
1. Implement DynamoDB at the very boundary of your application; the core sees only domain objects.
2. Keep indexing attributes (`PK`, `SK`, `GSI*`) separate from application attributes; never delete app attrs because they're encoded in keys.
3. Never reuse the same attribute across multiple indexes — always allocate fresh `GSI<N>PK`/`GSI<N>SK` per GSI.
4. Add a `Type` attribute on every item — it pays off during migrations and analytics.
5. Write small CLI debug scripts for every access pattern as you implement the data layer.
6. Abbreviate attribute names only at very large scale; the readability cost rarely justifies it.
7. Most DynamoDB work is design work — implementation is the relatively small last mile.

## Connects To
- **Ch 3**: Overloaded primary keys — implementation patterns built on top of that concept.
- **Ch 6**: Filter expressions on the Type attribute during ETL.
- **Ch 7-8**: Modeling decisions that this chapter operationalizes in code.
- **Ch 15**: Migration strategies — the Type attribute and boundary pattern make migrations tractable.
- **Ch 19-22**: End-to-end examples showing the boundary pattern at scale.
