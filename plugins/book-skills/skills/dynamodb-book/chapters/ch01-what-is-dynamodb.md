# Chapter 1: What is DynamoDB?

## Core Idea
DynamoDB is a fully-managed NoSQL database from AWS, built around a wide-column / key-value data model that delivers consistent single-digit millisecond performance at any scale — and it fits hyper-scale and serverless workloads better than relational databases do.

## Frameworks Introduced
- **Five Misconceptions checklist**: Run a project through these before rejecting DynamoDB.
  - "It's just a key-value store" (false: handles relations and complex patterns)
  - "It doesn't scale" (false: Tier-1 Amazon services run on it)
  - "It's only for enormous scale" (false: ideal for serverless too)
  - "You can't use it if the data model will change" (false: migrations are possible)
  - "It's schemaless so you don't need a schema" (false: enforce schema in app code)
- **Two-axis fit test**: Use DynamoDB when EITHER (a) hyper-scale (>100GB, billions of ops) is likely, OR (b) you are on hyper-ephemeral compute (Lambda). Either condition alone justifies it.

## Key Concepts
- **Wide-column store**: A hash table where each value is a B-tree — "bookshelf of phone books".
- **HTTP connection model**: Stateless requests; no connection pool needed; unlimited concurrency.
- **IAM authentication**: No DB users/passwords — AWS IAM roles and policies authorize requests, optionally down to specific actions, keys, or attributes.
- **Read Capacity Unit (RCU)**: 1 strongly-consistent read/sec OR 2 eventually-consistent reads/sec up to 4KB.
- **Write Capacity Unit (WCU)**: 1 write/sec up to 1KB.
- **On-Demand vs Provisioned**: On-demand = pay per request, higher unit price, no planning. Provisioned = cheaper, requires capacity planning.
- **DynamoDB Streams**: Built-in change data capture; transactional log of writes consumable by Lambda.
- **OLTP**: DDB's sweet spot — fast small reads/writes for end-user apps. Not OLAP.

## Mental Models
- Think of a DynamoDB table as a bookshelf (hash table) of phone books (B-trees) — partition key picks the book, sort key picks the page range.
- Use DynamoDB when your access patterns are predictable and you need linear scalability without operational burden.
- Treat HTTP+IAM as a feature, not a limitation — it is exactly what makes Lambda integration painless.
- Start On-Demand, switch to Provisioned once you understand your traffic baseline.

## Anti-patterns
- **Scans in user-facing access patterns**: kills the scalability story; only acceptable for exports or tiny tables.
- **Concentrating all data on one partition key**: defeats horizontal sharding; you'll hit throttles before throughput limits.
- **Skipping schema design because "NoSQL is schemaless"**: produces unreadable garbage in the table.
- **Using DDB without modeling access patterns first**: leads to bad primary key choices and costly migrations.

## Reference Tables

| Competitor | When DynamoDB wins | When the competitor wins |
|------------|-------------------|--------------------------|
| Relational (Postgres, MySQL, Aurora) | Hyperscale, serverless, predictable patterns | Rapid prototyping with unknown query patterns, ad-hoc analytics |
| MongoDB | Hyperscale with disciplined access patterns; managed-by-AWS | Flexible queries, text/geo/multi-key indexes, cloud portability |
| Cassandra | Want fully-managed wide-column with no ops team | Cloud portability is mandatory |

## Key Takeaways
1. DynamoDB excels in two specific scenarios — hyperscale and hyper-ephemeral compute — and is "good enough" for most OLTP.
2. RCU/WCU pricing is workload-based, not resource-based; read and write throughput scale independently.
3. The HTTP + IAM model is what makes DDB the default database for AWS Lambda.
4. Schemaless means the DB won't enforce a schema — your application still must.
5. DynamoDB Streams give you native CDC without extra infrastructure.
6. Avoid Cassandra unless you genuinely need cloud portability — operational cost is enormous.

## Connects To
- **Ch 2**: Core vocabulary (table, item, attribute, primary key, secondary index) operationalizes the model described here.
- **Ch 4**: The "no bad queries" API design enforces the scalability promise made in this chapter.
- **Designing Data-Intensive Applications (Kleppmann)**: Dynamo Paper inspired NoSQL movement; relax joins and strong consistency to shard infinitely.
