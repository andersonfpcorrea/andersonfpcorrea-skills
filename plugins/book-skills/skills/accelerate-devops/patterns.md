# Patterns — Accelerate: The Science of Lean Software and DevOps

## Pattern: Trunk-Based Development

**When to use**: Any team practicing continuous integration; especially when merging pain and long integration periods slow delivery.

**How**: Maintain <3 active branches at any time. All branches must have lifetimes <1 day before merging to trunk/master. No stabilization periods or code freezes. Feature flags handle incomplete features in production code.

**Trade-offs**: Requires disciplined small commits and fast automated test suites. Open-source projects with part-time contributors are a valid exception.

---

## Pattern: Deployment Pipeline

**When to use**: Any team wanting to implement CD; required for meeting compliance (segregation of duties) without a CAB.

**How**: Every change to version control triggers: automated build → fast unit tests → slower acceptance tests → (optional manual approval gate) → automated deploy to production. No human should be able to modify production without going through the pipeline.

**Trade-offs**: Significant upfront investment in test automation and infrastructure. Pays back quickly through reduced deployment pain and rework.

---

## Pattern: Lightweight Change Approval via Peer Review

**When to use**: Replace CAB processes; required for satisfying change management requirements without performance degradation.

**How**: One person not involved in authoring the change reviews it before/after commit to version control (pair programming or pull request review). Approval recorded in a system of record (GitHub PR approval or pipeline stage). Changes applied to production only through automated pipeline.

**Trade-offs**: Requires cultural investment in code review practices. Significantly faster than CAB with equivalent or better audit trail.

---

## Pattern: WIP Limits + Visual Displays + Monitoring Feedback Loop

**When to use**: Improving delivery performance and team culture; especially useful when teams are over-committed or work has invisible bottlenecks.

**How**: (1) Set WIP limits on work in flight — when a limit is hit, the team stops pulling new work and fixes the bottleneck instead. (2) Display current WIP, defect rates, and quality metrics on visual boards accessible to engineers and leaders. (3) Route application and infrastructure monitoring data into daily business decisions, not just incident alerting.

**Trade-offs**: WIP limits alone don't work — all three components together produce the performance effect.

---

## Pattern: Shift-Left Security

**When to use**: Organizations where security review is a late-stage bottleneck; regulated industries where compliance gates create delays.

**How**: Include InfoSec in application design reviews and feature demos. Test security features as part of automated test suites. Provide pre-approved security libraries, packages, and toolchains that make the secure path the easy path. Train developers on OWASP Top 10 and common vulnerabilities.

**Trade-offs**: Requires InfoSec team investment in developer enablement rather than gate-keeping. Upfront enablement work reduces total remediation time by ~50%.

---

## Pattern: Lean Product Development (Small Batches + Feedback + Experimentation)

**When to use**: Product teams wanting to reduce wasted development effort and improve customer outcomes.

**How**: Decompose features into pieces completable in <1 week. Build MVPs as learning vehicles. Actively seek and incorporate customer feedback in each cycle. Give development teams authority to change specifications in response to what they discover, without external approval.

**Trade-offs**: Requires organizational trust in teams; requires product management to let go of detailed upfront specification. Creates a virtuous cycle with CD — each improvement enables the other.

---

## Pattern: Inverse Conway Maneuver

**When to use**: When the existing team structure is producing a tightly coupled architecture that prevents independent deployment.

**How**: Define the target architecture first (loosely coupled, independently deployable services/components). Restructure teams around the target architecture boundaries — cross-functional teams owning each service end-to-end. Eliminate cross-team dependencies that would require coordination for a single team's deployment.

**Trade-offs**: Team restructuring is politically sensitive. Transition period creates temporary disruption. Long-term result: linear or better scaling of productivity with headcount.

---

## Pattern: Capability-Driven Investment (vs. Maturity Model)

**When to use**: Assessing DevOps progress; deciding where to invest improvement effort; communicating technology strategy to leadership.

**How**: For each of the 24 capabilities, measure current state using validated survey items. Identify capabilities with the largest gap and highest impact on your four key metrics. Invest in those specific capabilities; re-measure regularly; set targets for outcome metrics (the four DORA metrics).

**Trade-offs**: Requires investment in measurement infrastructure. Avoids the trap of declaring "we're at level 3" without measurable outcome improvement.

---

## Pattern: Transformational Leadership Investment

**When to use**: Technology leaders at any level wanting to improve team culture and delivery performance.

**How**: Practice the five dimensions consistently (vision, inspirational communication, intellectual stimulation, supportive leadership, personal recognition). Create concrete investment: training budgets, conference attendance, hack days, yak days, 20% time for experimentation. Remove obstacles; delegate authority to those with knowledge.

**Trade-offs**: Leadership without capable team practices still doesn't produce high performance — leadership amplifies, it doesn't substitute. Both are necessary.
