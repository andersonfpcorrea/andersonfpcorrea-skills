# Chapter 24: Distributed Periodic Scheduling with Cron

## Core Idea
Cron's single-machine failure domain is unacceptable at scale. Distributed cron requires consensus-based leader election and careful handling of the double-launch vs. skip tradeoff for non-idempotent jobs.

## Frameworks Introduced
- **Idempotency classification**: Before building distributed cron, classify each job:
  - **Idempotent**: Safe to run multiple times (GC, analytics). Double-launch is acceptable.
  - **Non-idempotent**: Must not run twice (email newsletter, payroll). Skip > double-launch.
  - **Skip-sensitive**: Cannot be skipped (payroll). Requires special handling.

- **"Fail closed" preference**: In ambiguous situations, prefer skipping a launch over risking a double-launch. Owner monitoring + manual re-run is recoverable; undoing a double-launch (newsletter, financial) may be impossible.

## Key Concepts
- **Distributed cron architecture**: Separate scheduler from workers. Scheduler runs on small number of machines (2–5) with consensus-based leader election. Worker nodes are the full datacenter fleet via Borg.
- **Single-machine cron failure domain problem**: In a 1,000-machine datacenter, one machine failure = 0.1% failure rate = entire cron service down.
- **State persistence requirements**: Only state needing persistence across restarts is the crontab config. Launch state is fire-and-forget by default (unlike anacron).
- **The leader election problem**: Distributed cron requires exactly one scheduler running at any time. Solution: Chubby/Zookeeper lease → only the lease holder launches jobs.
- **Race conditions on handoff**: When the leader fails and a new one takes over, some jobs may have been launched by the old leader but not yet recorded. New leader must handle this potential duplicate.
- **Monitoring cron jobs**: Owners must monitor their cron job effects independently — cron service cannot know if a job produced correct output, only that it was launched.
- **RPC latency for large-scale launches**: Launching a cron job in a datacenter = sending RPCs to Borg. Not instantaneous. Health check timeouts + scheduling delays mean job launch is not atomic.

## Mental Models
- Single-machine cron is a single point of failure. Distributed cron requires consensus to maintain exactly-one semantics.
- Prefer skips over double-launches for non-idempotent jobs — unrecoverable > missed.
- Cron job health monitoring is the owner's responsibility, not the scheduler's.

## Anti-patterns
- **Assuming cron launches are atomic**: RPC to Borg + machine selection + startup takes time. Jobs may appear "not running" while in transition.
- **Non-idempotent cron jobs without at-most-once tracking**: Any distributed scheduler will eventually cause double-launches under failure conditions.
- **No monitoring of cron job effects**: The scheduler only knows it launched; it cannot verify the job succeeded or produced correct output.

## Key Takeaways
1. Classify cron jobs as idempotent, non-idempotent, or skip-sensitive before designing the distribution strategy.
2. Use consensus-based leader election (Chubby/Zookeeper) for exactly-one scheduler guarantee.
3. Prefer skip over double-launch — recovering from a skipped launch is more tractable than undoing a double.
4. Owners must monitor their own cron job effects — the scheduler provides launch guarantees, not result guarantees.
5. Distributed cron is a research-grade problem; use proven solutions (Kubernetes CronJobs, cloud scheduler services) rather than building from scratch.

## Connects To
- **Ch 23**: Distributed consensus — the foundation for leader election in distributed cron.
- **Ch 5**: Eliminating toil — manual job scheduling is toil; cron automates it.
- **Ch 7**: Automation — distributed cron is automation infrastructure.
