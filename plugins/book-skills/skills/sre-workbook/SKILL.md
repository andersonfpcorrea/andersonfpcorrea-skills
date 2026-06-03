---
name: sre-workbook
description: "Knowledge base from \"The Site Reliability Workbook: Practical Ways to Implement SRE\" by Beyer, Murphy, Rensin, Kawahara & Thorne (Google). Use when implementing SLOs and error budgets, designing multiwindow multi-burn-rate alerting, establishing error budget policies, eliminating toil, running incident response (IMAG/ICS), canarying releases, or building and scaling SRE teams. This skill covers the practical lane: worked SLO examples, exact PromQL alerting tables, error-budget policy templates, toil measurement, canary methodology, and organizational change management for SRE."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, chapter number, or 'index' — e.g. 'burn rate alerting', 'ch05', 'SLO template']"
---

# The Site Reliability Workbook
**Editors**: Betsy Beyer, Niall Richard Murphy, David K. Rensin, Kent Kawahara & Stephen Thorne (Google) | **Pages**: ~508 | **Chapters**: 21 + 3 appendices | **Generated**: 2026-06-03

## How to Use This Skill

- **Without arguments** — load the core frameworks and critical tables below
- **With a topic** — ask about `SLO implementation`, `burn rate`, `error budget policy`, `canary`, `incident command`, `toil`, etc.; I find and read the relevant chapter
- **With chapter** — ask for `ch05` or `ch02`; I load that chapter file
- **Browse** — ask "what chapters do you have?" to see the full index

For deep dives beyond the core frameworks, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md` files.

---

## Core Frameworks & Mental Models

### 1. SLI = Good Events / Total Events (Ch 2) — the fundamental formula

Every SLI should be expressed as `good_events / total_events` (0–100%). This makes error budgets, alerting, and tooling uniform across all service types.

- **Error budget**: `100% − SLO_target`
- **Error budget (events)**: `total_events × (1 − SLO_target)`

SLI types by service component:
- Request-driven: **availability** (non-5XX / total), **latency** (< threshold / total), **quality** (undegraded / total)
- Pipeline: **freshness** (fresh reads / total reads), **correctness** (correct outputs / total prober), **coverage** (complete runs / total runs)
- Storage: **durability** (readable records / written records)

The four SLO prerequisites before you can use error budgets for decisions: (1) stakeholder-approved SLOs, (2) engineering team agrees they're achievable, (3) org commits to error-budget-driven decisions, (4) refinement process exists. Without these, SLOs are just KPIs.

### 2. Multiwindow, Multi-Burn-Rate Alerting (Ch 5) — the recommended approach

**Burn rate** = how fast the service consumes its error budget relative to the SLO period. Burn rate 1 = exactly exhausting budget at window end; burn rate 14.4 = exhausting in ~50h.

**Recommended parameters for 99.9% SLO (30-day window)**:

| Severity | Long window | Short window | Burn rate | Budget consumed | Threshold |
|----------|-------------|--------------|-----------|----------------|-----------|
| Page     | 1 hour      | 5 minutes    | 14.4      | 2%             | 0.0144    |
| Page     | 6 hours     | 30 minutes   | 6         | 5%             | 0.006     |
| Ticket   | 3 days      | 6 hours      | 1         | 10%            | 0.001     |

Short window = 1/12 of long window. Alert fires only when BOTH windows exceed threshold — this ensures budget is actively being consumed, not just recently was.

**Error threshold formula**: `burn_rate × (1 − SLO)`. For 99.9%: `burn_rate × 0.001`.

Six methods in the chapter: (1) raw error rate, (2) long window, (3) duration parameter, (4) single burn rate, (5) multiple burn rates, (6) multiwindow multi-burn-rate. Methods 1–3 are non-viable. Method 6 is the answer.

### 3. Error Budget Policy (Ch 2 + Appendix B) — the decision mechanism

The error budget policy formalizes what happens when the budget is exhausted:
- **Halt all non-P0 releases** until back within SLO.
- If cause was own code: **mandatory reliability work**.
- If cause was external (infrastructure, dependency): **may continue feature work**.
- Single incident >20% of budget: **mandatory postmortem** with at least one P0 action item.
- Escalation for disagreements: CTO/VP.

Without an error budget policy, SLOs are reporting metrics, not decision levers.

### 4. Incident Command System — IMAG Roles and 3Cs (Ch 9)

Three roles (IC, OL, CL), one hierarchy. IC commands and coordinates; does not debug.

**The 3Cs**: Coordinate, Communicate, Control.

**Declare early**: managed incidents resolve faster. If in doubt, declare. The IC assumes all roles not yet delegated — for small incidents, one person holds IC+OL+CL.

**Working document**: real-time notes of what's been tried, ruled out, current hypotheses. The working doc becomes the postmortem timeline.

### 5. Toil — Definition and 50% Cap (Ch 6)

Toil is repetitive, manual, automatable, reactive operational work that lacks enduring value and scales with the system. Google caps SRE operational work at 50% of total time.

**Measure first**: choose an objective unit (hours, tickets, patches), track before/during/after elimination efforts. ROI calculation must include indirect benefits (morale, fewer outages, career growth) — direct time savings alone often understate the value.

**Fix root causes**: masking symptoms removes the incentive for the owning team to fix the root cause.

### 6. Canarying (Ch 16) — limiting release blast radius

A canary is a partial (1–5%), time-limited deployment evaluated against a control (stable production). Compare canary vs. control metrics — not before/after historical comparison (external factors confound before/after).

A 1% canary reduces error-budget impact of a bad release by ~100×. Most incidents are caused by binary or configuration changes (~70% per Appendix C).

### 7. SRE Engagement Model — Service Lifecycle and PRR (Ch 18)

SRE engagement follows 7 lifecycle phases (Architecture → Active Dev → LA → GA → Deprecation → Abandoned → Unsupported). The Production Readiness Review (PRR) is the gate before SRE takes on-call at GA.

**PRR minimum requirements**: SLOs defined and measured; alerting configured; runbooks exist; error budget policy signed; on-call handoff process documented; capacity plan reviewed.

SRE value is highest when engaged at design phase (Phases 1–2); post-GA engagement can only patch architecture decisions that are already locked in.

### 8. SRE Team Lifecycle and ADKAR (Ch 20, Ch 21)

**Principle #1**: "SRE needs SLOs with consequences." Without this, SRE becomes ops rebranded.

**ADKAR gap diagnosis** (most common adoption failures):
- Awareness missing → publish incident cost data
- Desire missing → demonstrate personal benefit
- Knowledge missing → provide training and templates
- Ability missing → invest in tooling
- Reinforcement missing → implement error budget policy and ProdEx reviews

### 9. Simplicity as Reliability (Ch 7)

Simple systems break less often and are faster to fix. SREs, with end-to-end system visibility, are the natural champions for simplicity.

**Complexity proxies**: training time to on-call, explanation time for whiteboard overview, config diversity, deployment diversity.
**Simplification is a feature**: reserve explicit budget (e.g., 10% of engineering time) for simplification projects; treat them as deliverables.

### 10. NALSD — Non-Abstract Large System Design (Ch 12)

Start with one machine, use real numbers (QPS, bytes, latency). Add distribution only when a concrete bottleneck requires it. Evaluate failure modes at each design iteration. Reliability is the most critical feature — defer it and you accept fewer features at higher cost.

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-sre-vs-devops.md) | How SRE Relates to DevOps | SRE implements DevOps, 5 SRE axioms |
| [ch02](chapters/ch02-implementing-slos.md) | Implementing SLOs | SLI formula, error budget, worked example, SLO prerequisites |
| [ch03](chapters/ch03-slo-case-studies.md) | SLO Engineering Case Studies | VALET taxonomy, Evernote + Home Depot adoption stories |
| [ch04](chapters/ch04-monitoring.md) | Monitoring | Purposeful metrics, config-as-code, monitoring sources |
| [ch05](chapters/ch05-alerting-on-slos.md) | Alerting on SLOs | **6 methods, multiwindow multi-burn-rate, PromQL, burn rate table** |
| [ch06](chapters/ch06-eliminating-toil.md) | Eliminating Toil | Toil definition, 50% cap, measurement method, taxonomy |
| [ch07](chapters/ch07-simplicity.md) | Simplicity | Complexity proxies, Gall's Law, Hyrum's Law, simplification-as-feature |
| [ch08](chapters/ch08-on-call.md) | On-Call | Pager anatomy, rotation health, runbook requirement |
| [ch09](chapters/ch09-incident-response.md) | Incident Response | **IMAG roles, 3Cs, declare early, working document** |
| [ch10](chapters/ch10-postmortem-culture.md) | Postmortem Culture | Blameless, good vs. bad postmortem, action item quality |
| [ch11](chapters/ch11-managing-load.md) | Managing Load | GCLB layers, autoscaling, load shedding, Pokémon GO case study |
| [ch12](chapters/ch12-nalsd.md) | NALSD | Iterative design, capacity math, failure mode evaluation |
| [ch13](chapters/ch13-data-pipelines.md) | Data Processing Pipelines | Pipeline SLIs, idempotency, checkpointing, Spotify case study |
| [ch14](chapters/ch14-configuration-design.md) | Configuration Design | User-centric config, questions close to goals, safety mechanics |
| [ch15](chapters/ch15-configuration-specifics.md) | Configuration Specifics | 5 config pitfalls, Jsonnet, when to evaluate config |
| [ch16](chapters/ch16-canarying.md) | Canarying Releases | **Canary methodology, metrics selection, PromQL comparison, blue/green** |
| [ch17](chapters/ch17-overload-recovery.md) | Identifying and Recovering from Overload | Overload signals, mitigation strategies, team capacity planning |
| [ch18](chapters/ch18-sre-engagement-model.md) | SRE Engagement Model | **Service lifecycle 7 phases, PRR checklist, tiered engagement** |
| [ch19](chapters/ch19-sre-beyond-walls.md) | SRE: Reaching Beyond Your Walls | 6 platform truths, 5-step customer SRE, shared dashboards |
| [ch20](chapters/ch20-sre-team-lifecycles.md) | SRE Team Lifecycles | Maturity stages, Tuckman applied, Mission Control, ProdEx |
| [ch21](chapters/ch21-organizational-change.md) | Organizational Change Management | **ADKAR, Kotter, Deming applied to SRE adoption** |

### Appendices
- **Appendix A**: Complete example SLO Document (mobile game service: API + pipeline + storage SLIs)
- **Appendix B**: Complete example Error Budget Policy (trigger conditions, escalation, background)
- **Appendix C**: Postmortem analysis results (~70% of incidents caused by binary/config pushes)

---

## Topic Index

- **Alerting (burn rate, multiwindow)** → ch05, cheatsheet
- **ADKAR** → ch21
- **Availability SLI** → ch02, ch03
- **Blameless postmortem** → ch10
- **Burn rate** → ch05, cheatsheet
- **Canary / canarying** → ch16
- **Configuration design** → ch14, ch15
- **Correctness SLI** → ch02, ch13
- **Coverage SLI** → ch02, ch13
- **DevOps vs. SRE** → ch01
- **Error budget** → ch02, ch05, cheatsheet
- **Error budget policy** → ch02, Appendix B
- **Freshness SLI** → ch02, ch13
- **Gall's Law** → ch07
- **Hyrum's Law** → ch07
- **IC / OL / CL roles** → ch09
- **ICS / IMAG** → ch09
- **Incident declaration** → ch09
- **Incident response** → ch09
- **Jsonnet** → ch15
- **Latency SLI** → ch02, ch05
- **Load balancing** → ch11
- **Load shedding** → ch11
- **NALSD** → ch12
- **On-call** → ch08
- **Overload (team)** → ch17
- **Pipelines (data)** → ch13
- **Postmortem** → ch10
- **PRR (Production Readiness Review)** → ch18
- **PromQL alerting** → ch05, cheatsheet
- **Recall / Precision (alerting)** → ch05
- **Request class buckets** → ch05, cheatsheet
- **SLI** → ch02, ch03
- **SLI specification vs. implementation** → ch02
- **SLO** → ch02, ch03
- **SLO example (full)** → Appendix A
- **SRE engagement model** → ch18
- **SRE team lifecycle** → ch20
- **SRE vs. DevOps** → ch01
- **Simplicity** → ch07
- **Toil** → ch06
- **VALET** → ch03
- **Working document (incident)** → ch09

## Supporting Files

- [glossary.md](glossary.md) — all key terms with definitions (~60 terms)
- [patterns.md](patterns.md) — all concrete techniques with When/How/Trade-offs
- [cheatsheet.md](cheatsheet.md) — burn-rate alert table, error budget formulas, request class buckets, PromQL template

---

## Scope & Limits

This skill covers the 2018 edition of The Site Reliability Workbook. It does not include:
- Updates to Google's SRE practices post-2018
- Current versions of tools mentioned (Prometheus, Kubernetes, GCP)
- The companion volume "Site Reliability Engineering" (2016) — some concepts reference it but are not reproduced here
- Numerical pricing or quota information for GCP services

For implementation work, combine with current Prometheus/Kubernetes documentation and your organization's specific tooling. The SLO methodology, alerting formulas, and incident response principles are tooling-independent.
