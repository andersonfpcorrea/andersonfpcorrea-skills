# Chapter 10: The Importance of Strategies

## Core Idea
DynamoDB modeling is "more art than science" — unlike RDBMS where there is essentially one right way to model a relationship, DynamoDB offers multiple named strategies per problem, and the modeler must pick the right one for the situation. The next six chapters catalog those strategies.

## Key Concepts
- **Strategy**: A named, reusable recipe for a modeling problem (one-to-many, many-to-many, filtering, etc.). Each has tradeoffs and a "when to use" criterion.
- **RDBMS vs. DynamoDB modeling**: RDBMS — duplicate? normalize. One-to-many? foreign key. Many-to-many? join table. DynamoDB — there are 5 ways to model one-to-many alone; choice depends on access patterns.
- **Access-pattern-driven design**: "Attack your application data model one access pattern at a time, searching for the right strategy to solve the immediate problem."

## Mental Models
- Two engineers modeling the same RDBMS app will produce nearly identical schemas. Two engineers modeling the same DynamoDB app can produce vastly different tables — and both can be correct.
- Think of strategies as puzzle pieces. A real table almost always combines several strategies (Ch 21 GitHub example uses many in one table).

## The Five One-to-Many Strategies (preview of Ch 11)
1. Denormalize via a complex attribute (list/map)
2. Denormalize by duplicating data
3. Composite primary key + Query API
4. Secondary index + Query API
5. Composite sort keys with hierarchical data

## Key Takeaways
1. "Strategy" is DeBrie's deliberate vocabulary — each is a named recipe with documented tradeoffs, not just an idea.
2. Knowing the catalog of strategies is the prerequisite to data modeling; you cannot reason about access patterns without it.
3. Multiple strategies typically coexist in a single table — single-table design is composition of strategies.
4. Data modeling with DynamoDB requires flexibility, creativity, and persistence — expect iteration.

## Connects To
- **Ch 11-15**: Each catalogs strategies for one type of problem (one-to-many, many-to-many, filtering, sorting, migrations).
- **Ch 7-9**: Provided the data-modeling foundation and process; this chapter pivots from process to catalog.
- **Ch 19-21**: Worked examples that demonstrate combining strategies in production tables.
