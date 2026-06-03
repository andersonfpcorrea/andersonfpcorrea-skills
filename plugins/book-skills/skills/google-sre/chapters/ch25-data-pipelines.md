# Chapter 25: Data Processing Pipelines

## Core Idea
Periodic pipelines are fragile at scale. The leader-follower model (Google Workflow) replaces periodic batch jobs with a continuously-running pipeline that self-manages work distribution, handles failures, and scales without manual tuning.

## Frameworks Introduced
- **Pipeline depth**: Number of chained programs. Shallow = 1; deep = tens to hundreds. Deeper pipelines have more failure points and more complex debugging.
- **Google Workflow (leader-follower model)**: A master process (leader) manages work distribution; worker processes (followers) execute tasks. Leader tracks state; workers are stateless.
  - Key advantage: Dynamic work distribution replaces static chunking, eliminating uneven work distribution problems.
  - Leader is highly available via consensus-based failover.

## Key Concepts
- **Periodic pipeline failure modes**:
  1. **Hanging chunks**: One overloaded chunk blocks the entire pipeline stage (completion gated on worst case).
  2. **No checkpointing**: Killing a hanging job restarts all chunks from zero, wasting completed work.
  3. **Execution overlap**: At high frequency, new runs start before previous runs complete; stacks up or kills prior work.
  4. **Resource preemption**: Batch priority jobs are preempted by production jobs during high cluster load.
- **Continuous vs. periodic pipelines**: Continuous pipelines (never stop) avoid startup delays and resource preemption. Better for latency-sensitive data processing.
- **Uneven work distribution**: Partitioning by customer or entity creates chunks of vastly different size. The pipeline's end-to-end runtime is capped by the largest chunk.
- **Workflow execution stages**: (1) Inject data; (2) Distribute work units to workers; (3) Workers process; (4) Leader aggregates and advances. Each stage is checkpointed.
- **Business continuity**: Workflow supports cross-datacenter failover; if the primary datacenter is unavailable, a secondary datacenter can continue processing from the last checkpoint.

## Mental Models
- Periodic pipelines work fine when tuned. They break when growth changes the data distribution, job timing, or resource availability — which always happens eventually.
- The leader-follower model separates "what work needs to be done" (leader's state) from "how to do the work" (worker logic) — each can scale independently.
- "Reducing execution interval below the effective lower bound simply results in undesirable behavior rather than increased progress."

## Anti-patterns
- **Static chunking for dynamic data**: If data skew changes, static chunk sizes become uneven, causing hanging chunks.
- **No checkpointing**: Restarting failed jobs from scratch wastes all completed work. Always checkpoint intermediate results.
- **Periodic frequency as a proxy for freshness**: Pipeline latency ≠ execution frequency. Continuous pipelines often provide better latency at lower resource cost.
- **Batch priority for latency-sensitive pipelines**: Batch jobs are preempted. Use continuous pipelines or higher priority for time-sensitive data.

## Key Takeaways
1. Periodic pipelines are fragile under organic growth — hanging chunks, no checkpointing, execution overlap, and preemption are all systemic weaknesses.
2. The leader-follower model (Google Workflow) solves these with dynamic work distribution, checkpointing, and continuous operation.
3. Checkpoint intermediate pipeline state — restarting from zero on failure is unacceptable for long-running pipelines.
4. Continuous pipelines provide better latency than periodic pipelines at equivalent or lower cost for steady-state workloads.
5. Partition work dynamically based on actual data size, not static assumptions about chunk uniformity.

## Connects To
- **Ch 24**: Distributed cron — the scheduling mechanism that launches periodic pipelines.
- **Ch 23**: Distributed consensus — Workflow uses consensus for leader election and state management.
- **Ch 26**: Data integrity — pipeline failures can result in data loss or duplication.
