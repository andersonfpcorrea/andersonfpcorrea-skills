# Chapter 23: Managing Critical State: Distributed Consensus for Reliability

## Core Idea
Leader election, distributed locking, and critical shared state cannot be solved with heartbeats/timeouts — these are instances of the distributed consensus problem, which requires formally proven algorithms (Paxos/Raft). Use consensus-based systems like Chubby/Zookeeper.

## Frameworks Introduced
- **CAP Theorem**: A distributed system cannot simultaneously guarantee Consistency + Availability + Partition tolerance. Since network partitions are inevitable, you choose C or A under partition.
- **ACID vs. BASE**: ACID (relational): consistent but harder to scale. BASE (NoSQL): highly available, eventual consistency — but places complexity burden on developers to handle stale/inconsistent reads.
- **Consensus-based system architectures**:
  1. **Reliable replicated state machine**: Paxos or Raft; minority of nodes can fail; all surviving nodes have consistent state.
  2. **Reliable replicated datastore**: Layered on replicated state machine; provides key-value store with consistency.
  3. **Distributed barrier**: All workers wait at a point until all are ready; enables coordination of batch jobs.
  4. **Distributed queue with at-least-once delivery**: Messages guaranteed to be processed despite failures.

## Key Concepts
- **Split-brain problem**: Two leaders think they are the sole master — causes data corruption. Heartbeat-based leader election is fundamentally unsound (network slowness ≠ node death).
  - Solution: Use consensus-based leader election (Chubby, Zookeeper, etcd).
- **The impossibility result (FLP)**: A consensus algorithm cannot be both safe and live under asynchronous communication with one faulty process. Real systems are correct but not always live.
- **Quorum (majority)**: A quorum requires at least ⌊N/2⌋ + 1 of N nodes to agree. This ensures any two quorums overlap by at least one member — the basis of Paxos correctness.
- **Performance tradeoffs**:
  - Consensus adds latency (multiple rounds of network communication).
  - For write-heavy workloads, consider leader leases to allow local reads.
  - Batch multiple writes into one consensus round for throughput.
- **Monitoring consensus systems**: Watch for: leader election frequency, rate of Paxos rounds, latency of consensus operations, number of replicas participating.
- **Zookeeper/Chubby/etcd**: Purpose-built consensus services. Never reimplement Paxos from scratch for a specific use case — use these proven systems.

## Mental Models
- "Informal approaches to leader election can lead to outages and subtle, hard-to-fix data consistency problems."
- Network slow ≠ node dead. Any system that treats timeouts as definitive proof of death will have split-brain under slow networks.
- Consensus is to distributed systems what mutex is to multithreading — you need it whenever shared mutable state is involved.
- Eventual consistency shifts complexity from the DB to every developer who uses it. Avoid unless necessary.

## Anti-patterns
- **Heartbeat-based leader election**: Cannot distinguish slow network from dead node. Always leads to split-brain under the right failure conditions.
- **Custom Paxos implementations**: Nearly impossible to get right. Use Chubby, Zookeeper, or etcd.
- **Assuming eventual consistency is "good enough"**: "We find developers spend a significant fraction of their time building complex mechanisms to cope with eventual consistency" — this is a false economy.
- **No monitoring of consensus operations**: Leader election storms and Paxos contention are invisible without specific metrics.

## Reference Tables
| Use Case | Recommended Approach |
|---|---|
| Leader election | Chubby/Zookeeper lease |
| Distributed lock | Chubby/Zookeeper ephemeral node |
| Shared config | Chubby/Zookeeper data node |
| Replicated KV store | Spanner (global) or Bigtable + Paxos |
| Job coordination | Barrier protocol via Zookeeper |

## Key Takeaways
1. Never implement distributed consensus from scratch. Use Chubby, Zookeeper, or etcd — they are formally proven.
2. Heartbeats cannot distinguish slow network from dead node — heartbeat-based leader election creates split-brain.
3. CAP: you can't have all three (C, A, P) under partition. Decide explicitly which you trade.
4. Eventual consistency (BASE) is not free — it shifts the consistency problem to every developer using the system.
5. Monitor consensus operations: leader election rate, Paxos round latency, replica participation count.

## Connects To
- **Ch 2**: Chubby — uses Paxos for distributed consensus at Google.
- **Ch 26**: Data integrity — correct consensus is the foundation of durable writes.
- **Ch 24**: Distributed cron — built on consensus for leader election.
