---
name: google-sre
description: "Knowledge base from \"Site Reliability Engineering: How Google Runs Production Systems\" by Betsy Beyer, Chris Jones, Jennifer Petoff & Niall Murphy (Google). Use when applying SRE philosophy, setting SLOs/error budgets, managing on-call and toil, designing incident response, conducting postmortems, handling cascading failures, or referencing Google's operational frameworks. Lane: SRE philosophy, org/process, postmortems, toil, on-call, error budgets, monitoring, cascading failures, load balancing, data integrity."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, framework name, or chapter number — e.g. 'error budget', 'four golden signals', 'ch06']"
---

# Site Reliability Engineering: How Google Runs Production Systems
**Editors**: Betsy Beyer, Chris Jones, Jennifer Petoff, Niall Richard Murphy (Google)  |  **Pages**: ~550  |  **Chapters**: 34  |  **Generated**: 2026-06-03

## How to Use This Skill

- **Without arguments** — load core frameworks and mental models below
- **With a topic** — ask about `error budget`, `postmortem`, `cascading failures`, `on-call`; I find and read the relevant chapter
- **With chapter** — ask for `ch06` or `ch22`; I load that chapter file
- **Browse** — ask "what chapters do you have?" to see the full index

For details beyond the core frameworks, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md`.

---

## Core Frameworks & Mental Models

### 1. Error Budget — The Structural Resolution to Dev/SRE Conflict (Ch 1, 3)

```
error_budget = 1 − SLO_target
```

The error budget is the permitted unreliability per quarter. As long as budget remains, releases ship. When exhausted, releases freeze until budget replenishes.

- **Why it works**: Replaces politics with an objective shared metric. Dev and SRE co-own the budget.
- **The formula**: 99.99% SLO → 0.01% budget → at 2.5M req/day, 250 errors allowed.
- **100% is always wrong**: Users cannot distinguish 99.999% from 100%. The marginal effort is wasted. Match reliability to what the business needs.
- **Availability = `successful_requests / total_requests`** (Google's preferred over uptime for distributed systems).

### 2. The Four Golden Signals (Ch 6)

If you can only measure four things on a user-facing system, measure these:

| Signal | What it measures | Leading indicator of |
|---|---|---|
| **Latency** | Time to service a request | User experience, saturation |
| **Traffic** | Demand (req/s, I/O rate) | Scaling needs |
| **Errors** | Rate of failed requests | Service health |
| **Saturation** | How "full" the system is | Impending failure |

Page on symptoms (what users see), not causes. Latency increases are the leading indicator of saturation.

### 3. The 50% Engineering Cap (Ch 1, 5)

SRE ops work (on-call, tickets, manual tasks) ≤ 50% of total time. Excess overflows to the dev team.

- **Why it works**: Creates feedback pressure on devs to build self-managing systems. Without the cap, toil expands to 100%.
- **On-call sub-rule**: On-call ≤ 25% of total time. Minimum rotation: 8 SREs (single-site), 6/site (multi-site).
- **Max incidents**: 2 per 12-hour shift. More → underlying system problem, not an individual performance issue.
- **"Hope is not a strategy"**: Every policy decision must be grounded in data.

### 4. Toil Definition (Ch 5)

Toil = operational work that is: **manual + repetitive + automatable + tactical + no enduring value + O(n) with service growth**. Any three attributes = strong signal. All six = definitely toil.

Toil is not overhead (meetings, HR) and not the first or second time you do something. It is the N-th repetition that could be automated.

### 5. Blameless Postmortem (Ch 15)

A postmortem is not punishment — it is a learning artifact. Blameless = focusing on systemic causes, not individual fault.

**Write one when**: user-visible downtime, data loss, on-call intervention, long MTTR, or monitoring failure.

**Structure**: Timeline → Impact → Root cause(s) → Contributing factors (systemic) → Action items with owners. Review with senior engineers. Share broadly.

**"You can't fix people, but you can fix systems and processes."**

### 6. Incident Management — The Incident Command System (Ch 14)

Four roles, recursive separation:
- **Incident Commander**: Holds high-level state; assigns roles; NEVER touches production.
- **Ops Lead**: ONLY person who modifies the system during the incident.
- **Communications**: Stakeholder updates + live incident document.
- **Planning**: Logistics, handoffs, bug tracking.

Only Ops Lead touches production. Freelancing by well-meaning engineers makes incidents worse. Handoffs require explicit verbal acknowledgment.

### 7. Troubleshooting Process (Ch 12)

1. **Triage first** — stabilize before root-causing. "Fly the airplane."
2. **Hypothetico-deductive**: form hypothesis → predict observable implications → test.
3. **Negative results are magic** — ruling out a cause is as valuable as confirming one.
4. **Think horses, not zebras** — common failures first; verify don't assume.

### 8. Cascading Failure Defense (Ch 22)

Cascade = positive feedback loop. When 10% of servers survive, dropping to 90% of normal load won't stabilize — drop to 10%.

**Prevention priority**:
1. Load test to known failure point
2. Serve degraded results
3. Reject requests early (load shedding)
4. Rate-limit at higher layers
5. Capacity planning at N+2

**Retry rules**: Always use randomized exponential backoff + jitter. Budget retries globally. 3 layers × 3 retries = 27× amplification at the database.

### 9. Automation Hierarchy (Ch 7)

1. Manual → 2. Personal script → 3. Shared script → 4. System-internal → **5. System self-manages** (the goal)

Automation is a force multiplier, not a panacea. Better than automation is a system designed to not need it.

### 10. SLO Selection Principles (Ch 4)

- **Start from what users care about**, not what is easy to measure.
- **Use percentiles**, not averages (p95/p99/p99.9 for latency).
- **Keep a safety margin**: internal SLO tighter than published external SLO.
- **Don't overachieve**: users build on actual behavior; Chubby planned outages demonstrate this.
- **Five rules**: don't anchor to current performance, keep simple, avoid absolutes, use few SLOs, start loose.

### 11. Monitoring Philosophy (Ch 6)

Three valid monitoring outputs: Alert (immediate action), Ticket (days), Log (no action needed).

Alert quality checklist: urgent + actionable + user-visible + novel + requires intelligence. Any violation → redesign or eliminate.

Monitoring systems that page must be **simple, predictable, and reliable**. Complex thresholds are fragile.

### 12. Release Engineering Principles (Ch 8)

1. **Hermetic builds**: same inputs → same outputs, always.
2. **High velocity**: frequent small releases reduce change size → easier debugging.
3. **Progressive rollout**: start 1 cluster → exponential expansion → 100%.
4. **Configuration is code**: config changes go through the same release process as binary changes.

~70% of outages are change-induced. Hermetic builds + progressive rollout + fast rollback are the answer.

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-introduction.md) | Introduction | Error budget, 50% cap, hope-is-not-a-strategy |
| [ch02](chapters/ch02-production-environment.md) | Production Environment at Google | Borg, BNS, N+2 redundancy, Google stack |
| [ch03](chapters/ch03-embracing-risk.md) | Embracing Risk | Error budget formula, availability metrics, risk tiering |
| [ch04](chapters/ch04-slo.md) | Service Level Objectives | SLI/SLO/SLA, percentiles, safety margin, SLO rules |
| [ch05](chapters/ch05-eliminating-toil.md) | Eliminating Toil | Toil definition, 50% rule, toil taxonomy |
| [ch06](chapters/ch06-monitoring.md) | Monitoring Distributed Systems | Four Golden Signals, symptoms vs. causes, alert quality |
| [ch07](chapters/ch07-automation.md) | Evolution of Automation | Automation hierarchy (5 levels), MoB case study |
| [ch08](chapters/ch08-release-engineering.md) | Release Engineering | Hermetic builds, progressive rollout, Rapid, config mgmt |
| [ch09](chapters/ch09-simplicity.md) | Simplicity | Essential vs. accidental complexity, negative LOC, minimal APIs |
| [ch10](chapters/ch10-practical-alerting.md) | Practical Alerting (Borgmon) | Time-series monitoring, varz, hierarchical topology |
| [ch11](chapters/ch11-being-on-call.md) | Being On-Call | 25% rule, 2 incidents/shift, follow-the-sun, cognitive load |
| [ch12](chapters/ch12-troubleshooting.md) | Effective Troubleshooting | Hypothetico-deductive, triage first, negative results |
| [ch13](chapters/ch13-emergency-response.md) | Emergency Response | 3 emergency types, rollback testing, out-of-band comms |
| [ch14](chapters/ch14-managing-incidents.md) | Managing Incidents | ICS (IC/Ops/Comms/Planning), live incident doc, handoff |
| [ch15](chapters/ch15-postmortem-culture.md) | Postmortem Culture | Blameless postmortem, triggers, Wheel of Misfortune |
| [ch16](chapters/ch16-tracking-outages.md) | Tracking Outages | Outalator, alerts vs. incidents, tagging taxonomy |
| [ch17](chapters/ch17-testing-reliability.md) | Testing for Reliability | Zero MTTR, test hierarchy, canary analysis, config tests |
| [ch18](chapters/ch18-sw-engineering-sre.md) | Software Engineering in SRE | Intent-based capacity planning, Auxon case study |
| [ch19](chapters/ch19-load-balancing-frontend.md) | Load Balancing at the Frontend | DNS LB, VIP, EDNS0, geographic routing |
| [ch20](chapters/ch20-load-balancing-datacenter.md) | Load Balancing in the Datacenter | Lame duck state, subsetting, WRR, CPU as capacity |
| [ch21](chapters/ch21-handling-overload.md) | Handling Overload | Adaptive throttling formula, criticality levels, deadline propagation |
| [ch22](chapters/ch22-cascading-failures.md) | Addressing Cascading Failures | Cascade triggers, defense-in-depth, retry amplification, GC spiral |
| [ch23](chapters/ch23-distributed-consensus.md) | Distributed Consensus | CAP, split-brain, Paxos/Raft, ACID vs. BASE |
| [ch24](chapters/ch24-distributed-cron.md) | Distributed Periodic Scheduling | Idempotency classification, fail-closed, leader election |
| [ch25](chapters/ch25-data-pipelines.md) | Data Processing Pipelines | Periodic pipeline failures, Google Workflow, checkpointing |
| [ch26](chapters/ch26-data-integrity.md) | Data Integrity | Proactive detection + rapid repair, soft deletion, backup vs. replication |
| [ch27](chapters/ch27-reliable-launches.md) | Reliable Product Launches | LCE, launch checklist, gradual rollout, NORAD Santa example |
| [ch28](chapters/ch28-accelerating-sres.md) | Accelerating SREs to On-Call | Wheel of Misfortune, education blueprint, anti-patterns |
| [ch29](chapters/ch29-dealing-with-interrupts.md) | Dealing with Interrupts | Pages/tickets/ops categories, flow state, bystander effect |
| [ch30](chapters/ch30-embedding-sre.md) | Embedding an SRE | 3-phase intervention, ops mode diagnosis, kindling |
| [ch31](chapters/ch31-communication-collaboration.md) | Communication and Collaboration | Production meetings, two-masters model, API-as-contract |
| [ch32](chapters/ch32-sre-engagement-model.md) | The Evolving SRE Engagement Model | PRR, early engagement, SRE platform, engagement spectrum |
| [ch33](chapters/ch33-lessons-from-other-industries.md) | Lessons from Other Industries | 4 universal themes, aviation/nuclear/telecom parallels |
| [ch34](chapters/ch34-conclusion.md) | Conclusion | 747 analogy, two-pilot model, enduring principles |

---

## Topic Index

- **Adaptive throttling** → ch21
- **Alert quality / alert fatigue** → ch6, ch10, ch11
- **Automation hierarchy** → ch7
- **Availability formulas** → ch3
- **Bigtable / Spanner / Chubby** → ch2, ch23
- **Borg / BNS** → ch2
- **Blameless postmortem** → ch15
- **CAP theorem / ACID / BASE** → ch23
- **Capacity planning** → ch1, ch18
- **Cascading failures** → ch22
- **Configuration management** → ch8
- **Consensus / Paxos / Raft** → ch23
- **Data integrity** → ch26
- **Data pipelines** → ch25
- **Deadline propagation** → ch21, ch22
- **Distributed cron** → ch24
- **Emergency response** → ch13
- **Embedding SRE** → ch30
- **Error budget** → ch1, ch3, ch4
- **Four Golden Signals** → ch6
- **Hermetic builds** → ch8
- **Incident management / ICS** → ch14
- **Intent-based capacity planning** → ch18
- **Lame duck state** → ch20
- **Launch coordination (LCE)** → ch27
- **Load balancing (frontend/DNS/VIP)** → ch19
- **Load balancing (datacenter/subsetting)** → ch20
- **Monitoring philosophy** → ch6
- **On-call (rules, rotation)** → ch11
- **Overload handling** → ch21
- **Playbooks** → ch1, ch11
- **Postmortem culture** → ch15
- **PRR (Production Readiness Review)** → ch32
- **Progressive rollout** → ch8
- **Release engineering** → ch8
- **Retry patterns / retry amplification** → ch21, ch22
- **SLI / SLO / SLA** → ch4
- **Simplicity / accidental complexity** → ch9
- **Software engineering in SRE** → ch18
- **SRE engagement model** → ch32
- **SRE training (Wheel of Misfortune)** → ch15, ch28
- **Testing for reliability** → ch17
- **Time-series monitoring (Borgmon)** → ch10
- **Toil / 50% cap** → ch1, ch5
- **Tracking outages (Outalator)** → ch16
- **Troubleshooting methodology** → ch12

---

## Supporting Files

- [glossary.md](glossary.md) — ~60 key terms with definitions and chapter references
- [patterns.md](patterns.md) — 17 named techniques with When/How/Trade-offs
- [cheatsheet.md](cheatsheet.md) — availability table, key formulas, decision tables, anti-patterns

---

## Scope & Limits

This skill covers the 2016 first edition of the SRE book. It does not include:
- Post-2016 Google infrastructure changes
- The SRE Workbook (companion volume with worked examples)
- Specific third-party tools or cloud provider implementations
- Current industry benchmarks or pricing

For hands-on implementation, combine with project-specific tools and current cloud provider documentation. The core principles (error budgets, blameless culture, the 50% cap, four golden signals) remain authoritative.
