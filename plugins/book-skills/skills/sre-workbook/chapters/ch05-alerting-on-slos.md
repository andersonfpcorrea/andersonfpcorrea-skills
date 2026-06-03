# Chapter 5: Alerting on SLOs

## Core Idea
Six progressively better ways to alert on SLO violations; the recommended approach is multiwindow, multi-burn-rate alerting (Method 6), which simultaneously achieves good precision, recall, detection time, and reset time.

## Frameworks Introduced

### Alerting Quality Dimensions
- **Precision**: % of fired alerts that were significant events (low precision = alert fatigue)
- **Recall**: % of significant events that triggered an alert (low recall = missed outages)
- **Detection time**: how long until an alert fires after the event starts
- **Reset time**: how long after an event ends the alert keeps firing (long reset = confusion)

### The Six Methods (in order of fidelity)

| Method | Approach | Fatal Flaw |
|--------|----------|------------|
| 1 | Error rate ≥ SLO threshold (10m window) | Poor precision; 144 alerts/day possible while meeting SLO |
| 2 | Error rate over 36h window | Very poor reset time (36h) |
| 3 | Error rate for 1m with 1h `for:` duration | Poor recall; 100% outage and 0.2% outage both wait 1h |
| 4 | Single burn rate alert (1h window) | Low recall: 35x burn never alerts but exhausts budget in 20.5h |
| 5 | Multiple burn rates (page + ticket) | Long reset time from 3d window; alert suppression needed |
| **6** | **Multiwindow, multi-burn-rate** | **Most parameters to manage (see Alerting at Scale)** |

### Burn Rate Concept
Burn rate = how fast the service consumes error budget relative to the SLO window.
- Burn rate 1 = consuming budget exactly; budget exhausted at window end
- Burn rate 14.4 = exhausting 30-day budget in ~50 hours

**Formula — time to exhaust budget:**
```
time_to_exhaustion = (1 - SLO) / error_rate
```

**Formula — budget consumed by time alert fires (burn-rate alert):**
```
budget_consumed = (burn_rate × alerting_window) / period
```

## Reference Table: Recommended Multi-Burn-Rate Parameters (99.9% SLO)

| Severity | Long window | Short window | Burn rate | Budget consumed |
|----------|-------------|--------------|-----------|----------------|
| Page     | 1 hour      | 5 minutes    | 14.4      | 2%             |
| Page     | 6 hours     | 30 minutes   | 6         | 5%             |
| Ticket   | 3 days      | 6 hours      | 1         | 10%            |

**Short window = 1/12 of long window** — reduces false positives by confirming the burn is still active.

## Reference Table: Burn Rates at 99.9% SLO

| Burn rate | Error rate | Time to exhaustion |
|-----------|------------|-------------------|
| 1         | 0.1%       | 30 days           |
| 2         | 0.2%       | 15 days           |
| 10        | 1%         | 3 days            |
| 1,000     | 100%       | 43 minutes        |

## PromQL: Method 6 — Multiwindow, Multi-Burn-Rate

```yaml
# Recording rule (Prometheus): compute error ratio over multiple windows
record: job:slo_errors_per_request:ratio_rate5m
expr: |
  sum(rate(slo_errors[5m])) by (job)
  /
  sum(rate(slo_requests[5m])) by (job)

# Page-level alerts (2% budget in 1h or 5% in 6h)
- alert: SLOBurnRatePage
  expr: |
    (
      job:slo_errors_per_request:ratio_rate1h{job="myjob"} > (14.4 * 0.001)
      and
      job:slo_errors_per_request:ratio_rate5m{job="myjob"} > (14.4 * 0.001)
    )
    or
    (
      job:slo_errors_per_request:ratio_rate6h{job="myjob"} > (6 * 0.001)
      and
      job:slo_errors_per_request:ratio_rate30m{job="myjob"} > (6 * 0.001)
    )
  severity: page

# Ticket-level alert (10% budget in 3d)
- alert: SLOBurnRateTicket
  expr: |
    (
      job:slo_errors_per_request:ratio_rate3d{job="myjob"} > (1 * 0.001)
      and
      job:slo_errors_per_request:ratio_rate6h{job="myjob"} > (1 * 0.001)
    )
  severity: ticket
```
*Multiply burn rate by `(1 - SLO)` to get the error threshold. For 99.9% SLO: threshold = burn_rate × 0.001.*

## Request Buckets for Alerting at Scale

Group request types into 5 buckets to avoid per-service alerting toil:

| Class | Availability | Latency p90 | Latency p99 |
|-------|-------------|-------------|-------------|
| CRITICAL | 99.99% | 100ms | 200ms |
| HIGH_FAST | 99.9% | 100ms | 200ms |
| HIGH_SLOW | 99.9% | 1,000ms | 5,000ms |
| LOW | 99% | None | None |
| NO_SLO | None | None | None |

## Low-Traffic Service Strategies
1. Generate synthetic traffic (black-box probers) to produce signal
2. Combine related low-traffic services into a single SLO group
3. Modify clients to retry with exponential backoff (reduces user impact per failure)
4. Lower the SLO or increase the window to match actual user-impact thresholds

## Anti-patterns
- **Method 3 (`for:` duration)**: Duration timer resets on any momentary recovery; a service oscillating at SLO boundary never pages.
- **Per-service custom burn rates**: Does not scale beyond a handful of services; creates cognitive overload.
- **Ignoring reset time**: Long reset times (36h window) mean engineers ignore active alerts.

## Key Takeaways
1. Method 6 (multiwindow, multi-burn-rate) is the recommended approach.
2. Short window = 1/12 long window; alerts only fire when budget is actively being consumed.
3. For 99.9% SLO: page at 14.4x burn (1h/5m), 6x burn (6h/30m); ticket at 1x burn (3d/6h).
4. For high-availability goals (99.999%+), alerting cannot defend the SLO — design must prevent the outage.

## Connects To
- **Ch 2**: Error budget definition and SLO targets this chapter consumes
- **Ch 4**: Monitoring metrics and recording rules that feed these alerts
- **Ch 9**: On-call receives the page alerts from this system
