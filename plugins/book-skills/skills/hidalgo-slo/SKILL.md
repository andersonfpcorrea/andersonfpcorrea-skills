---
name: hidalgo-slo
description: "Knowledge base from \"Implementing Service Level Objectives\" by Alex Hidalgo. Use when applying Hidalgo's frameworks for meaningful SLIs, reliability targets, error budget math and policy, SLO alerting, statistics behind SLOs, data reliability, or building SLO culture. This is the deepest practical SLI/SLO/error-budget treatment available — it covers the full Reliability Stack from measurement design through probability/statistics, alerting architecture, and organizational change."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, framework name, chapter number, or 'index']"
---

# Implementing Service Level Objectives
**Author**: Alex Hidalgo  |  **Pages**: ~646  |  **Chapters**: 17 + 2 appendices  |  **Generated**: 2026-06-03

## How to Use This Skill

- **Without arguments** — load core frameworks and mental models below
- **With a topic** — ask about `burn rate`, `error budget policy`, `percentiles`, `data reliability`, etc.; I find and read the relevant chapter
- **With chapter** — ask for `ch08` or `ch09`; I load that specific chapter
- **Browse** — ask "what chapters do you have?" to see the full index

For details beyond the core frameworks below, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md` files.

---

## Core Frameworks & Mental Models

### 1. The Reliability Stack — The Foundational Framework (Ch 1)

Three-layer hierarchy for user-centric reliability:

**SLI** (Service Level Indicator) — A measurement of service behavior from the user's perspective, expressed as a ratio: `good_events / total_events`. Not CPU usage or queue depth — user-observable outcomes.

**SLO** (Service Level Objective) — The target percentage for the SLI ratio. Good SLO: exceeding it means users are happy; missing it means users are unhappy. Not a contract — it can and should change.

**Error Budget** — `(1 − SLO_target)` × window = permitted unreliability. If SLO = 99.9%, the error budget = 0.1% of all events (or ~43 minutes per 30-day month).

SLA is a contractual version of SLO with financial consequences. This book focuses on SLOs, not SLAs.

The three service truths:
1. Reliability is the most important operational requirement.
2. Users determine reliability — not your logs.
3. Nothing is perfect; 100% is impossible and undesirable.

### 2. Meaningful SLIs — Start From the User Journey (Ch 3)

An SLI is fundamentally a user journey. Product teams call it a KPI; QA calls it an interface test. They're the same thing in different language.

**Measure many things by measuring only a few**: Verify the user gets correct data at the edge, and you've implicitly verified the service is up, available, responsive, format-correct, and returning good results. Use the highest-level user-observable outcome.

Six questions for any request/response service SLI:
1. Is it up? 2. Is it available? 3. Is it responsive? 4. Are good responses sufficient vs. errors? 5. Is the format correct? 6. Is the data correct?

Write every SLI as a plain English sentence: "When users search for a product, 99.8% of searches return a response within 4 seconds." If a non-engineer can't understand it, rewrite it.

### 3. Choosing Good SLO Targets (Ch 4)

**Don't constrain to "nines"**: 99.7%, 98.3%, 97.2% are as valid as 99.9% if data-driven. The "number nine" convention is an artifact, not a requirement.

**Dependency math**: `system_reliability = component_reliability^N`. Forty components at 99.9% compose to 96%. You cannot exceed your hard dependencies' reliability.

**The "too reliable" problem**: If you routinely exceed your SLO by a wide margin, users build higher expectations, engineers stop learning from failures, and you lose the freedom to experiment.

**Start from past performance**: Use the P95 or P99 of historical data as a starting target; adjust based on user feedback and metric quality (resolution, quantity, noise).

**Bad time formula**: `bad_seconds_per_day = (1 − SLO_target) × 86400`

| Target | Bad time/month | Practical on-call? |
|---|---|---|
| 99.99% | 4 m 23 s | Barely; needs auto-remediation |
| 99.95% | 21 m 54 s | Yes, with fast response |
| 99.9% | 43 m 50 s | Yes, standard |
| 99.7% | 2 h 9 m | Yes, comfortable |
| 99% | 7 h 18 m | Yes, for lower-criticality |

### 4. Error Budget Math and Policy (Ch 5)

**Events-based**:
```
budget = (1 - SLO) × total_requests
remaining_pct = (budget - failures) / budget
```

**Time-based** (30-day, 1-second resolution):
```
total = 2,592,000 seconds
budget = (1 - SLO) × 2,592,000
remaining = budget - bad_seconds
```

**Which to use**: Events-based for burn rate alerting; time-based for human communication. Use both.

**Rolling windows** (recommended): Bad events expire as they age out. **Calendar windows**: Budget resets on a date — risk of release flood at reset.

**Error budget policy**: Write it before you need it. Define what actions to take at 20%, 50%, and 100% budget consumption. Without a pre-agreed policy, error budget data goes unused. This is the cultural acid test.

**Budget uses beyond feature freeze**: Chaos engineering, load tests, blackhole exercises (intentional location failures), library/config experiments. Use surplus budget to learn about your systems.

### 5. SLO Alerting on Burn Rate (Ch 8)

Simple threshold alerting (CPU, queue depth) fails because: thresholds decay as the system grows, they're poor proxies for user experience, they lose context, and they generate alert fatigue.

**Two-alert architecture** (the gold standard):
- **Fast burn** (page): 2% budget consumed in 1 hour
- **Slow burn** (ticket): 10% budget consumed in 3 days

**Burn rate formula**:
```
burn_rate = observed_errors / allowable_errors
# > 1 → violating SLO at this rate
```

**The SLO alert condition**:
```
SUM(errors) / SUM(all_requests) > (1.0 - SLO_target)
```
over a window substantially shorter than the SLO evaluation period.

**Human response requires ≥5 minutes**. SLO targets above 99.95% on monthly windows leave <5 minutes for a complete outage response — auto-remediation or architecture change needed.

### 6. The Statistics Behind SLOs (Ch 9)

**For new services or low-QPS services**: Raw percentage calculations are unreliable with sparse data. Use Bayesian methods with a prior, or widen the measurement window.

**Multi-datacenter independence math**:
```
P(both DCs fail) = (1 − p)²
# Two DCs at 99% → system failure = 0.0001 → 99.99% composite
```
Only valid if failures are truly independent (cross-region, separate power/network).

**Latency and queueing**: At high utilization, queue wait → ∞. Latency SLOs must account for service utilization approaching capacity.

**For durability**:
```
P(all N replicas fail) = p_fail^N
# Three replicas at 0.1% annual failure rate → 10^-9 annual data loss probability
```
Same-DC replicas are NOT independent — correlated failure modes exist.

### 7. Data Reliability SLOs (Ch 11)

Seven data properties to consider for SLOs: **Freshness** (not the same as age), **Completeness**, **Consistency**, **Accuracy**, **Validity**, **Integrity**, **Durability**.

Critical distinction: **Durability failures are permanent**. All other SLO violations are recoverable. Invest disproportionately in preventing durability failures.

Design tensions: Completeness vs. Freshness, Consistency vs. Availability, Durability vs. Performance. SLOs make these trade-offs explicit.

### 8. SLO Culture — Crawl → Walk → Run (Ch 13, 16)

Culture change is 80% of the work. Technical implementation takes days; organizational alignment takes months.

**Six-step path**: get buy-in → prioritize → implement → use → iterate → advocate.

**The first error budget policy is the cultural acid test**: If the team ignores it when budget is exhausted, the SLO program is decorative.

**SLO advocacy phases**:
- **Crawl**: Education, artifacts, one pilot SLO, first training.
- **Walk**: Early adopters, case studies, train trainers.
- **Run**: Community of experts, SLOs in standard launch checklists.

First people, then processes, then technology.

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-the-reliability-stack.md) | The Reliability Stack | SLI/SLO/Error Budget, Service Truths, SLA vs SLO |
| [ch02](chapters/ch02-how-to-think-about-reliability.md) | How to Think About Reliability | Implied agreements, Hyrum's Law, reliability cost curve |
| [ch03](chapters/ch03-developing-meaningful-slis.md) | Developing Meaningful SLIs | User journey as SLI, measure many by measuring few, six questions |
| [ch04](chapters/ch04-choosing-good-slos.md) | Choosing Good SLOs | Five Ms, percentiles, dependency math, not-just-nines |
| [ch05](chapters/ch05-how-to-use-error-budgets.md) | How to Use Error Budgets | Error budget math, rolling vs calendar windows, budget policy |
| [ch06](chapters/ch06-getting-buy-in.md) | Getting Buy-In | Stakeholder matrix, objection responses, cultural test |
| [ch07](chapters/ch07-measuring-slis-and-slos.md) | Measuring SLIs and SLOs | Metrics vs logs, design goals, measurement patterns |
| [ch08](chapters/ch08-slo-monitoring-and-alerting.md) | SLO Monitoring and Alerting | Burn rate, fast/slow burn, why thresholds fail |
| [ch09](chapters/ch09-probability-and-statistics.md) | Probability and Statistics | Bernoulli, Binomial, Poisson, multi-DC math, Bayesian |
| [ch10](chapters/ch10-architecting-for-reliability.md) | Architecting for Reliability | SLO-first design, MTTR, hardware trade-offs |
| [ch11](chapters/ch11-data-reliability.md) | Data Reliability | 13 data properties, durability permanence, data lineage |
| [ch12](chapters/ch12-a-worked-example.md) | A Worked Example | SLIs as user journeys, Wiener Shirt-zel multi-service example |
| [ch13](chapters/ch13-building-an-slo-culture.md) | Building an SLO Culture | Six-step path, error budget policy as cultural test |
| [ch14](chapters/ch14-slo-evolution.md) | SLO Evolution | Seven change drivers, aspirational SLOs, revisit schedules |
| [ch15](chapters/ch15-discoverable-and-understandable-slos.md) | Discoverable and Understandable SLOs | SLO definition documents, phraseology, discoverability tooling |
| [ch16](chapters/ch16-slo-advocacy.md) | SLO Advocacy | Crawl/Walk/Run, artifacts, stakeholder pitch templates |
| [ch17](chapters/ch17-reliability-reporting.md) | Reliability Reporting | Why MTTX fails, error budget as reporting unit, audience matrix |

---

## Topic Index

- **Alert fatigue** → ch08
- **Alerting (SLO-based)** → ch08
- **Aspirational SLOs** → ch14
- **Bayesian inference** → ch09
- **Binomial distribution** → ch09
- **Brownfield transition** → ch08
- **Burn rate** → ch08, ch05
- **Buy-in** → ch06, ch13
- **Calendar vs rolling windows** → ch05, ch08
- **Compound reliability** → ch04, ch09, ch10
- **Data lineage** → ch11
- **Data properties (13)** → ch11
- **Dependency math** → ch04, ch09
- **Discoverability** → ch15
- **Durability** → ch09, ch11
- **Error budget** → ch01, ch05, ch08
- **Error budget math** → ch05
- **Error budget policy** → ch05, ch06, ch13
- **Experiments / chaos engineering** → ch05
- **Freshness (data)** → ch11
- **Hard vs soft dependencies** → ch04
- **Hyrum's Law** → ch02
- **Implied agreements** → ch02
- **Incident counting / MTTX (why bad)** → ch17
- **Low QPS services** → ch04, ch09, ch14
- **Measurement infrastructure** → ch07
- **Multi-datacenter SLO math** → ch09
- **Percentiles** → ch04
- **Poisson distribution** → ch09
- **Reporting** → ch17
- **Reliability Stack** → ch01
- **Rolling windows** → ch05, ch08
- **SLA vs SLO vs SLI** → ch01
- **SLI definition** → ch01, ch03
- **SLI measurement** → ch07
- **SLO culture** → ch13, ch16
- **SLO definition document** → ch15, appendix A
- **SLO evolution** → ch14
- **SLO target selection** → ch04
- **Statistics** → ch04, ch09
- **Threshold alerting (why bad)** → ch08
- **User journey** → ch03, ch12
- **Worked example** → ch12

## Supporting Files

- [glossary.md](glossary.md) — ~65 terms, alphabetical with chapter references
- [patterns.md](patterns.md) — 12 patterns with When/How/Trade-offs
- [cheatsheet.md](cheatsheet.md) — All key formulas, decision tables, anti-patterns quick reference

---

## Scope & Limits

This skill covers the book content only (O'Reilly, 2020). It does not include:
- SLO tooling released after 2020 (Sloth, OpenSLO, etc.)
- Current vendor-specific SLO implementations (Datadog, Prometheus Operator, etc.)
- SRE organizational structures (see Google's SRE books for that)

For hands-on implementation, combine with your monitoring stack's documentation. The book's conceptual frameworks (Reliability Stack, user-journey SLIs, error budget policy, burn rate alerting) remain authoritative. This is the deepest single-source treatment of practical SLI/SLO implementation.
