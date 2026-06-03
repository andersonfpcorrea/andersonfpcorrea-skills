# Google SRE Patterns & Techniques

## Error Budget Management

**When to use**: When dev and SRE conflict over release velocity vs. reliability.
**How**: Set quarterly SLO → monitor weekly → `error_budget = 1 − SLO`. While budget remains, releases ship. When exhausted, freeze releases until budget replenishes.
**Trade-offs**: Requires trust and management support to enforce the freeze. Creates shared incentives between dev and SRE.

---

## The 50% Engineering Cap

**When to use**: When SRE team is spending > 50% on ops work.
**How**: Measure ops time (on-call, tickets, manual tasks). If > 50%, overflow to dev team (reassign bugs, put devs on on-call rotation). End when ops load drops to ≤ 50%.
**Trade-offs**: Creates friction with product dev. Essential for keeping SRE from becoming a pure ops team.

---

## Blameless Postmortem

**When to use**: After any significant incident (user-visible downtime, data loss, manual intervention, long MTTR, monitoring failure).
**How**: Write within 48–72 hours. Timeline → impact → root cause(s) → contributing factors (systemic, not individual) → action items with owners and deadlines. Review with senior engineers. Share broadly.
**Trade-offs**: Time cost. Requires management support to maintain blameless culture vs. blame culture pressure.

---

## The Four Golden Signals (SLI Selection)

**When to use**: When choosing what to monitor for any user-facing service.
**How**: Instrument and alert on: Latency (p99, not mean), Traffic (req/s), Errors (rate of failures), Saturation (most constrained resource). Use black-box for paging, white-box for debugging.
**Trade-offs**: Simple but covers the vast majority of real production problems.

---

## Alert Quality Checklist

**When to use**: Before creating any new alert.
**How**: Ask: (1) Does this detect urgent, actionable, user-visible condition? (2) Will I ever ignore it? (3) Can I take action? (4) Is the action urgent? (5) Could it be automated? If any answer is "no/yes-but," redesign or eliminate the alert.
**Trade-offs**: Slower alert creation, but prevents alert fatigue and pager burnout.

---

## Progressive Rollout Pattern

**When to use**: For any change to user-facing production systems.
**How**: 1 cluster → monitor for N minutes → double affected clusters → monitor → repeat until 100%. Define rollback criteria before starting. Automate the go/no-go signal.
**Trade-offs**: Slower full deployment. Dramatically reduces blast radius of bad pushes.

---

## Hermetic Build

**When to use**: For any software that must be reproducible (i.e., all production software).
**How**: Pin compiler version, dependency versions, and build tool versions in the build configuration. Builds must not depend on host environment. Use a build tool like Bazel.
**Trade-offs**: More complex build setup. Enables bisecting, cherry-picking, and reproducing prod builds for debugging.

---

## Automation Hierarchy (Prescriptive)

**When to use**: When deciding how to address a manual operational task.
**How**: Diagnose current level (1–5). Target next level. Don't skip levels.
1. Manual → 2. Personal script → 3. Shared script → 4. System-internal script → 5. System self-manages.
**Trade-offs**: Level 5 requires product team cooperation. Each step reduces toil; not all teams can reach level 5 immediately.

---

## Lame Duck Shutdown

**When to use**: For any backend service that handles long-lived requests or has clients that must reroute.
**How**: On SIGTERM: enter lame duck state → broadcast to all clients via UDP health checks → drain in-flight requests → exit cleanly. Do NOT accept new requests after entering lame duck.
**Trade-offs**: Requires implementing SIGTERM handler and RPC framework cooperation. Eliminates "request errors during push."

---

## Adaptive Throttling (Client-Side)

**When to use**: When a backend service is being overwhelmed with rejections that consume resources.
**How**: Each client tracks `requests` (attempted) and `accepts` (accepted) over 2-minute window. Local rejection probability = `max(0, (requests − K × accepts) / (requests + 1))`. Typical K = 2.
**Trade-offs**: Requires client-side code change. Dramatically reduces backend overload from rejection processing.

---

## Deadline Propagation

**When to use**: For all multi-tier RPC systems.
**How**: Root request sets deadline N. Each downstream RPC gets `remaining = N − time_elapsed_so_far`. Servers check remaining deadline before each processing stage and abandon work if deadline has passed.
**Trade-offs**: Requires RPC framework support. Prevents backends from doing work for clients who have already given up.

---

## Per-Customer Quota Enforcement

**When to use**: For shared backend services with multiple clients.
**How**: Negotiate CPU-seconds/second quota per customer. Monitor global usage in real time. When a customer exceeds quota: return "quota exceeded" fast, not by doing the full work. Protect well-behaved customers from misbehaving ones.
**Trade-offs**: Requires quota negotiation with each customer. Prevents single tenant from cascading failures to all.

---

## SLO Choosing Process

**When to use**: When setting objectives for a new or existing service.
**How**: (1) Start from what users care about. (2) Work backward to measurable SLIs. (3) Apply 5 rules: don't pick from current performance, keep simple, avoid absolutes, use few SLOs, start loose. (4) Set internal SLO tighter than published external SLO (safety margin). (5) Never consistently exceed your SLO floor.
**Trade-offs**: Political — SLOs create accountability. Better to have imperfect SLOs than none.

---

## Incident Command System

**When to use**: For any incident that has more than one person involved.
**How**: Designate IC (who manages, never touches prod), Ops Lead (only person touching prod), Comms Lead (stakeholder updates + incident doc), Planning Lead (bugs, logistics, handoffs). Live incident document. Explicit verbal handoffs.
**Trade-offs**: Requires role familiarity. Without it, unmanaged incidents spiral.

---

## The Wheel of Misfortune

**When to use**: When training new SREs before their first on-call shift.
**How**: Select a historical postmortem → assign roles → run the scenario with original IC present → debrief on what was done right/wrong → update runbooks based on gaps found.
**Trade-offs**: Time investment. The highest-ROI SRE training technique.

---

## Intent-Based Capacity Planning

**When to use**: When capacity planning for services with geographic, latency, or redundancy constraints.
**How**: Encode requirements (SLOs, latency targets, geographic constraints, redundancy needs) in machine-readable format. Use a solver to generate optimal resource allocation. When requirements change, re-solve.
**Trade-offs**: Requires upfront modeling effort. Dramatically reduces manual bin-packing toil and produces better allocations.

---

## Consensus-Based Leader Election

**When to use**: Whenever distributed systems need a single master (database, cron scheduler, distributed lock).
**How**: Use Chubby, Zookeeper, or etcd — do NOT implement Paxos from scratch. Acquire a lease; renew it. Treat expiration as trigger for new election.
**Trade-offs**: Consensus adds latency. Correct; never causes split-brain. Worth the latency cost for correctness.

---

## Proactive Data Integrity

**When to use**: For any service that stores user data.
**How**: Layer 1: Soft deletion with configurable retention. Layer 2: Backup + point-in-time recovery. Layer 3: Cross-zone replication. Layer 4: Regular integrity checks (checksums). Layer 5: Audit logging. Set explicit recovery SLO (24h is a starting point).
**Trade-offs**: Storage + operational cost. Protects against the worst-case scenario: irreversible data loss.
