# Chapter 17: Data Modeling Examples (Intro)

## Core Idea
A short transition chapter that sets up the worked examples in the remaining chapters. DeBrie tells you to read those chapters with two goals in mind: (1) internalize the **process** from Chapter 7, and (2) collect the **strategies** from Chapters 10-16 so you can mix and match them when modeling your own application. No example will perfectly match your domain — the value is in the techniques, not the templates.

## Frameworks Introduced

- **The data modeling process (applied)**: The four-step loop from Chapter 7, repeated for every example.
  - When to use: every new DynamoDB application.
  - How:
    1. Understand your application (what are you building, what are the constraints).
    2. Create your ERD (entities and relationships, no attributes).
    3. Understand your access patterns (list the read/write paths your application needs).
    4. Design the primary key and secondary indexes that satisfy those access patterns.

- **Strategy reuse over example reuse**: Treat each example as a source of techniques, not a copy-paste model.
  - When to use: while reading the examples and while modeling your own data.
  - How: when you see a pattern (e.g. parent-in-middle for two relational access patterns, reference counts, KSUID sort keys), tag it as a tool. Later, recognize the *shape* of the problem in your own work and reach for the matching tool.

## Key Concepts
- **ERDs without attributes**: DeBrie deliberately omits attributes from his ERDs. Reasons: large models (10+ entities, 15+ relationships) become unreadable; attributes are an application concern, not a relationship concern. The ERD's job is to show *how entities relate*.
- **Examples omit basic access patterns**: Trivial Read/Write/Update on individual items aren't listed unless something interesting is happening. Focus your attention on the patterns DeBrie *does* call out.
- **Indexing attributes vs. application attributes** (from Ch 9): in the modeling phase, only indexing attributes matter. Items shown in the examples will hide application attributes to reduce visual noise — that doesn't mean those attributes are absent in production.
- **Active reading**: Try to build the data model alongside DeBrie before reading his solution. Your approach won't match his exactly, and that's the point — you'll learn more from the divergence than from passive reading.

## Mental Models
- An ERD answers "how do these things relate?" not "what fields do they have?"
- An access-pattern list answers "what does my application need to do?" — it's the contract the primary key must satisfy.
- A data model is the *intersection* of the ERD and the access pattern list, mediated by DynamoDB's primary-key constraints.
- Strategies are reusable; examples are not.

## Anti-patterns
- Reading the examples for templates to copy. Read them for techniques to recognize.
- Expecting your data model to look exactly like DeBrie's even if you have a similar domain. Different access patterns produce different models.
- Including every attribute on the ERD — it adds noise without adding modeling value.
- Skipping the "try it yourself first" step before reading the walkthrough.

## Reference Tables

| Example format element | Purpose |
|---|---|
| Problem statement + mockups | Get into a developer's head; understand what's being built |
| Access pattern list | The contract the table design must satisfy |
| ERD (no attributes) | Show entity relationships, nothing else |
| Final table design | Show indexing attributes only; application attributes hidden |

## Key Takeaways
- The examples are the most important chapters in the book — they're where strategies become muscle memory.
- Process beats memorization: walk the four steps every time.
- Build alongside DeBrie before reading his solution.
- Harvest strategies. The next data model you build will be a combination of techniques you saw across multiple examples.

## Connects To
- Chapter 7 (the data modeling process being applied)
- Chapter 9 (indexing vs. application attributes — why examples hide some attributes)
- Chapters 10-16 (the strategies catalogued for use in the examples)
- Chapters 18-22 (the worked examples themselves: Session Store, E-commerce, Big Time Deals, GitHub Migration, etc.)
