# Cheatsheet — Implementing Service Level Objectives

## Core Formulas

### SLI (ratio form)
```
SLI = good_events / total_events
```

### Error Budget (events-based)
```
budget_total_pct = 1 - SLO_target
remaining = budget_total_pct - actual_failure_pct
remaining_fraction = remaining / budget_total_pct
```

### Error Budget (time-based, 30-day, 1-second resolution)
```
total_seconds = 60 × 60 × 24 × 30 = 2,592,000
budget_seconds = (1 - SLO_target) × total_seconds
remaining = budget_seconds - bad_seconds
```

### Burn Rate
```
burn_rate = observed_errors / allowable_errors
# > 1 → consuming budget faster than allowed
```

### Compound Reliability (N serial components)
```
system_reliability = component_reliability ^ N
```

### Independent Multi-Datacenter
```
P(both fail) = (1 - p) ^ num_datacenters
# Two DCs at 99% → system failure probability = 0.0001 (99.99% composite)
```

### Bad time per window
```
bad_seconds = (1 - SLO_target) × window_seconds
```

---

## SLO Target to Bad Time (per 30-day month)

| Target | Bad time/month | Notes |
|---|---|---|
| 99.999% | 26.3 s | Infeasible for human response |
| 99.99% | 4 m 23 s | Auto-remediation required |
| 99.95% | 21 m 54 s | Human response barely feasible |
| 99.9% | 43 m 50 s | Standard achievable with on-call |
| 99.7% | 2 h 9 m 36 s | Common for batch/internal services |
| 99% | 7 h 18 m | Appropriate for low-criticality services |
| 95% | 36 h | Very low criticality or aspirational baseline |

---

## Target Selection Decision Guide

| Situation | Guidance |
|---|---|
| No historical data | Start with 99.5%–99.9%; revise after 30 days of observation |
| Existing data available | Use P99 of observed performance as starting point |
| Many hard dependencies | Apply compound math; you can't exceed dependency reliability |
| Low QPS (< 10 req/hr) | Use wider time window; single failures skew percentages |
| Data service | Add freshness, completeness SLOs; durability is non-negotiable |
| Customer-facing product | Use 90-day or yearly window; users remember failures > 30 days |
| Too many SLOs | Cut to 3–5 per service; multiple comparison problem |

---

## Two-Alert Rule of Thumb

| Alert type | Window | Budget burn trigger | Severity |
|---|---|---|---|
| Fast burn | 1 hour | 2% budget consumed | Page |
| Slow burn | 3 days | 10% budget consumed | Ticket |

---

## SLO vs. SLA vs. SLI Quick Reference

| | SLI | SLO | SLA |
|---|---|---|---|
| **What** | Measurement | Target | Contract |
| **Expressed as** | Ratio / % | % with time window | % with consequences |
| **Changes** | As service evolves | Freely, with process | Requires renegotiation |
| **Violation result** | Data for discussion | Decision trigger | Compensation owed |
| **Audience** | Engineers | Entire org | Customers / Legal |

---

## Data Properties Quick Reference (Ch 11)

**Data properties**: Freshness, Completeness, Consistency, Accuracy, Validity, Integrity, Durability

**Data application properties**: Security, Availability, Scalability, Performance, Resilience, Robustness

**Warning**: Durability failures are permanent. All others are potentially recoverable.

---

## Anti-Pattern Quick Reference

| Anti-pattern | Consequence | Fix |
|---|---|---|
| Alert on CPU/memory | Alert fatigue; poor user-experience correlation | Alert on SLI/error budget |
| Target 100% | Impossible; infinite cost; kills learning | Target user happiness threshold |
| Target only "nines" | Arbitrary; may miss real user needs | Use data-derived targets like 98.3% |
| Too many SLOs | Multiple comparison noise; confuses stakeholders | 3–5 per service |
| No error budget policy | Error budget data goes unused | Write policy before deploying SLO |
| Calendar windows + freeze | Release flood at reset | Use rolling windows; think in budgets not freezes |
| Undocumented SLOs | Not discoverable; can't hold teams accountable | Template + wiki + service catalog |

---

## The Reliability Stack in One Sentence

Measure user experience with SLIs → set a target with an SLO → track permitted unreliability with an error budget → use that data to decide when to ship vs. when to stabilize.
