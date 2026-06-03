# Chapter 19: Building an E-commerce Application

## Core Idea
Customer and order management for an e-commerce site (modeled on ThriftBooks). Introduces primary-key overloading, item-collection pre-joins, multi-attribute uniqueness via transactions, and the choice between denormalizing a one-to-many vs. modeling it as separate items.

## Domain & Entities
The slice of the system being modeled covers `Customer`, `Address`, `Order`, and `OrderItem`. A Customer has many Addresses (bounded — capped at ~20), a Customer places many Orders (unbounded), and an Order contains many OrderItems (unbounded). A separate `CustomerEmail` helper item is introduced to enforce email uniqueness — it isn't in the ERD but emerges from the modeling.

## Access Patterns
- **Create Customer (unique on both username AND email)**: `TransactWriteItems` writing a `Customer` item AND a `CustomerEmail` item, each guarded by `attribute_not_exists(PK)`. If either uniqueness check fails, the whole transaction aborts.
- **Create / Update / Delete Mailing Address for Customer**: `UpdateItem` against the Customer item, mutating the `Addresses` map attribute. No separate Address items exist.
- **View Customer & Most Recent Orders**: Query main table by `PK = CUSTOMER#<Username>` with `ScanIndexForward=False` and `Limit=11` — returns the Customer item plus 10 most recent Orders in a single round trip.
- **Place Order**: `TransactWriteItems` that writes the Order plus all its OrderItems atomically.
- **Update Order**: `UpdateItem` on a single Order item (e.g., changing status).
- **View Order & Order Items**: Query `GSI1` by `GSI1PK = ORDER#<OrderId>` — returns the Order and every OrderItem belonging to it.

## Table Design
Composite primary key (`PK`, `SK`) on the main table, with one GSI (`GSI1`) used specifically to relocate Orders and OrderItems into a shared item collection. Two key tricks: the `#` prefix on Order sort keys puts them BEFORE the Customer item lexicographically so descending queries hit Orders first, and Addresses are denormalized into a map attribute on the Customer (the relationship is bounded, so it fits in 400 KB).

Main table:

| PK | SK | Type | Key attrs |
|---|---|---|---|
| `CUSTOMER#alexdebrie` | `CUSTOMER#alexdebrie` | Customer | `Username`, `Name`, `Addresses` (Map) |
| `CUSTOMEREMAIL#alex@x.com` | `CUSTOMEREMAIL#alex@x.com` | CustomerEmail | (uniqueness guard only) |
| `CUSTOMER#alexdebrie` | `#ORDER#1Vrg...` | Order | `OrderId`, `Status`, `Total`, `GSI1PK`, `GSI1SK` |
| `CUSTOMER#alexdebrie` | `#ORDER#1Aab...` | Order | ... |
| `ORDER#1Vrg...#ITEM#abc` | `ORDER#1Vrg...#ITEM#abc` | OrderItem | `GSI1PK`, `GSI1SK` |

GSI1 (re-groups Order with its OrderItems):

| GSI1PK | GSI1SK | Type |
|---|---|---|
| `ORDER#1Vrg...` | `ORDER#1Vrg...` | Order |
| `ORDER#1Vrg...` | `ITEM#abc` | OrderItem |
| `ORDER#1Vrg...` | `ITEM#def` | OrderItem |

## Key Techniques Used
- **Multi-attribute uniqueness via tracking items (Ch 16)**: a second `CustomerEmail` item lives alongside the Customer, sharing nothing structurally — it exists solely so a condition expression can enforce email uniqueness.
- **Pre-joining via shared partition key (Ch 11)**: Orders share the Customer's PK so "fetch customer + recent orders" is one Query.
- **Sort-key prefix manipulation (Ch 11)**: prefixing Order SKs with `#` (lexicographically before `C` in `CUSTOMER#`) places Orders before the Customer item, so a descending Query starting from the Customer naturally returns the most recent Orders.
- **KSUIDs (Ch 14)**: `OrderId` is a KSUID — globally unique AND chronologically sortable, eliminating the need for a separate timestamp in the sort key.
- **Denormalize bounded one-to-many as a complex attribute (Ch 11)**: Addresses live on the Customer as a map because the count is bounded and they're never queried independently.
- **Use a GSI to re-group items into a DIFFERENT item collection (Ch 12)**: Orders sit with Customers on the base table for one access pattern AND sit with their OrderItems on GSI1 for a different access pattern.
- **Transactions for cross-item atomic writes (Ch 3, 16)**: both "create customer + email guard" and "place order + create items" are TransactWriteItems.

## Notable Code Examples
```python
resp = client.query(
    TableName='EcommerceTable',
    KeyConditionExpression='#pk = :pk',
    ExpressionAttributeNames={'#pk': 'PK'},
    ExpressionAttributeValues={':pk': {'S': 'CUSTOMER#alexdebrie'}},
    ScanIndexForward=False,
    Limit=11,
)
```
- **What it demonstrates**: a single Query returns the Customer plus the 10 most recent Orders — the `#` prefix on Order SKs places them lexicographically before the Customer, so descending order yields Orders newest-first followed by the Customer item at position 11.

## Anti-patterns Avoided
- **Don't denormalize Orders onto the Customer.** Item size cap is 400 KB and you don't want to throttle order growth. Reserve denormalization for bounded relationships like Addresses.
- **Don't share an item collection between Orders AND OrderItems on the base table.** Putting OrderItems in the Customer's partition would balloon the "fetch customer + recent orders" Query with hundreds of irrelevant items per page. Push the Order↔OrderItem join into GSI1 instead.
- **Don't try to enforce multi-attribute uniqueness with a single item.** A single item can only guarantee uniqueness on its PRIMARY KEY (one composite of attributes), so two independently-unique attributes need two separate tracking items + a transaction.
- **Don't reverse the sort by reading then sorting in app code.** Use `ScanIndexForward=False` natively.

## Lessons For Your Own Models
1. **The "parent + recent children" pattern is THE canonical use of composite keys.** Same PK for parent and children; `Limit` + descending Query in one round trip.
2. **A `#` (or `0`) prefix is a deliberate sort-order hack** — choose it whenever you want a single item to sort before or after a class of siblings.
3. **The same entity can appear in multiple item collections.** That's the whole point of GSIs — relocate items into the grouping a particular access pattern needs.
4. **Bounded one-to-many → map attribute. Unbounded one-to-many → separate items.** That binary covers most of your modeling decisions.
5. **Document your entity chart and your access-pattern table in the repo.** Future developers should NEVER have to reverse-engineer your key schema from the data access layer.

## Connects To
- **Ch 11**: one-to-many strategies — denormalize-with-complex-attribute (Addresses) vs. parent-in-partition (Orders) vs. secondary-index regrouping (OrderItems).
- **Ch 12**: secondary indexes and overloaded GSIs.
- **Ch 14**: KSUIDs for chronologically-sortable unique IDs.
- **Ch 16**: multi-attribute uniqueness via tracking items inside a transaction.
- **Ch 3**: TransactWriteItems mechanics and failure semantics.
