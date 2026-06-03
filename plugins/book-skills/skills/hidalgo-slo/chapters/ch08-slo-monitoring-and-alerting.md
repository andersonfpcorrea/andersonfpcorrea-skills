# Chapter 8: SLO Monitoring and Alerting

## Core Idea
Simple threshold alerting on internal metrics (CPU, queue depth) is fundamentally broken — thresholds decay, context is lost, and alert fatigue grows. SLO-based alerting on error budget burn rate is the current gold standard.

## Why Simple Threshold Alerting Fails

1. **Thresholds don't stay relevant**: A business growing 4–10%/quarter will see alert thresholds become meaningless within a year.
2. **Poor proxies for user experience**: CPU usage correlates with latency but is not latency. Measuring the wrong thing produces irrelevant alerts.
3. **Context loss**: Static thresholds don't adapt to traffic patterns (low-traffic periods trigger noise; high-traffic periods miss real issues).
4. **Alert fatigue**: Noisy alerts reduce human alerting efficacy over time. More alerts → less action → worse outcomes.
5. **Fog of war**: During outages, hundreds of threshold alerts fire simultaneously, obscuring the real cause.

## SLO Alerting Architecture

### Two classes of problem require two alert types:
- **Fast burn** (100% outage or near-complete failure): Short window, high sensitivity. Alert quickly.
- **Slow burn** (steady drip of errors accumulating over days): Longer window, lower sensitivity. Alert before budget is exhausted.

### Burn Rate Formula:
```
burn_rate = observed_errors_per_period / allowable_errors_per_period
```
- burn_rate > 1 → consuming budget faster than allowed
- Target: alert before burn_rate hits 1 continuously

### Worked Example (99.9% SLO, 30-day window):
- Total budget = 100% − 99.9% = 0.1% of events = 2,592 seconds of downtime
- Fast burn alert: 1% budget loss in 1 hour → 25 seconds of 100% outage in 1 hour triggers page
- Slow burn alert: 10% budget loss in 1 week → 259 seconds in a week triggers ticket

### Rule of Thumb (from Google's Site Reliability Workbook):
- Page alert: 2% budget burn in 1 hour
- Ticket alert: 10% budget burn in 3 days

## SLO Window × Target Decision Table

| Target | 30-day budget (seconds) | Practical SLO window |
|---|---|---|
| 99.9999% (six nines) | 2.59 | >1 year (human response impossible) |
| 99.999% (five nines) | 25.9 | >1 year |
| 99.99% (four nines) | 259 | >quarter (human response barely feasible) |
| 99.95% | 1,296 | Month–quarter |
| 99.9% (three nines) | 2,592 | Month–quarter (standard) |
| 99.5% | 12,960 | Week–month |
| 99% (two nines) | 25,920 | Week–month |

Human response requires ≥5 minutes of lead time. SLO targets of 99.99% on monthly windows leave only ~4 minutes of human response time for a complete outage — effectively infeasible.

## SLO Alert Condition (algebraic form):
```
SUM(errors) / SUM(all_requests) > (1.0 - SLO_target)
```
over a window substantially shorter than the SLO evaluation window.

## Implementing in a Brownfield Environment

1. **Show the human cost**: Alert fatigue data, burnout metrics, incident correlation with noisy alerts.
2. **Audit existing alerts**: For each alert, record how often it fired, its "real severity" vs. declared severity, its outage footprint.
3. **Run old and new in parallel**: Maintain both systems; suppress pages from the old system but keep it visible for comparison.
4. **Set a sunset date** for the old system — otherwise you now have two problems.

## Troubleshooting with SLO Alerting

- **Fast-burn alert firing**: Use standard diagnostics (logs, traces, dashboards) to identify the cause.
- **Slow-burn alert firing**: Requires breadth-first search across request dimensions. Use observability tooling (distributed tracing) to find what distinguishes failing requests from succeeding ones. Depth-first search wastes time.

## Corner Cases

- **Very low event count** (e.g., 10 events/period): Cannot have 99% SLO — minimum allowable error = 1/N. Consider recasting the SLO over the underlying data volume, not batch count.
- **Very high targets** (five or six nines): Human response is infeasible; auto-remediation or architecture redesign required.

## Anti-patterns

- **Alerting on CPU, memory, or queue depth** instead of SLIs.
- **Setting thresholds based on "the last outage value"**: Causes thresholds to creep down over time.
- **Dual alert deduplication failure**: Two windows may page twice for the same event; implement deduplication.
- **No brownfield transition plan**: Adding SLO alerts without reducing old alerts creates double noise.

## Key Takeaways

1. Alert on error budget burn rate, not internal system metrics.
2. You need two alert types: fast-burn (short window, high sensitivity) and slow-burn (long window, lower sensitivity).
3. Human response requires ≥5 minutes; SLO targets above 99.95% on monthly windows need auto-remediation.
4. Slow-burn troubleshooting requires observability tooling (tracing), not just dashboards.
5. In brownfield environments, run old and new in parallel with a committed sunset date.

## Connects To

- **Ch 5**: Error budget math that feeds the burn rate calculation
- **Ch 7**: The measurement infrastructure that generates the event data
- **Ch 9**: Statistical tools for handling edge cases in burn rate calculation
