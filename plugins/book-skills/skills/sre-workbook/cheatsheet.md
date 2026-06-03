# Cheatsheet — The Site Reliability Workbook

## Multiwindow Multi-Burn-Rate Alert Table (99.9% SLO, 30-day window)

| Severity | Long window | Short window | Burn rate | Budget consumed | Error threshold |
|----------|-------------|--------------|-----------|----------------|----------------|
| Page     | 1 hour      | 5 minutes    | 14.4      | 2%             | 0.001 × 14.4 = 0.0144 |
| Page     | 6 hours     | 30 minutes   | 6         | 5%             | 0.001 × 6    = 0.006  |
| Ticket   | 3 days      | 6 hours      | 1         | 10%            | 0.001 × 1    = 0.001  |

Short window = 1/12 of long window. Both windows must exceed threshold for alert to fire.

**Formula**: `error_threshold = burn_rate × (1 - SLO)` (e.g., `14.4 × 0.001 = 0.0144`)

---

## Burn Rate Reference Table (99.9% SLO)

| Burn rate | Error rate | Time to full exhaustion |
|-----------|------------|------------------------|
| 1         | 0.1%       | 30 days                |
| 2         | 0.2%       | 15 days                |
| 6         | 0.6%       | 5 days                 |
| 10        | 1%         | 3 days                 |
| 14.4      | 1.44%      | ~50 hours              |
| 1,000     | 100%       | 43 minutes             |

---

## SLI Types Quick Reference

| Component | SLI | Formula |
|-----------|-----|---------|
| Request-driven | Availability | `success_requests / total_requests` |
| Request-driven | Latency | `requests_below_threshold / total_requests` |
| Request-driven | Quality | `undegraded_responses / total_responses` |
| Pipeline | Freshness | `reads_with_fresh_data / total_reads` |
| Pipeline | Correctness | `correct_outputs / total_prober_outputs` |
| Pipeline | Coverage | `complete_runs / total_runs` |
| Storage | Durability | `readable_records / written_records` |

---

## Error Budget Formula

```
error_budget_events = total_events × (1 - SLO_target)
error_budget_% = 1 - SLO_target

Example: 1M requests, 99.9% SLO
  error_budget = 1,000,000 × 0.001 = 1,000 allowed errors
```

---

## Incident Response Roles

| Role | Does | Does NOT |
|------|------|----------|
| IC (Incident Commander) | Commands, coordinates, communicates, delegates | Debug/mitigate directly |
| OL (Operations Lead) | Mitigates, debugs, applies fixes | Communicate externally |
| CL (Communications Lead) | Status updates, handles inquiries | Debug/mitigate |

**3Cs**: Coordinate, Communicate, Control.

---

## Request Class Buckets (Alerting at Scale)

| Class | Availability | p90 latency | p99 latency |
|-------|-------------|-------------|-------------|
| CRITICAL | 99.99% | 100ms | 200ms |
| HIGH_FAST | 99.9% | 100ms | 200ms |
| HIGH_SLOW | 99.9% | 1,000ms | 5,000ms |
| LOW | 99% | none | none |
| NO_SLO | none | none | none |

---

## Canary vs. Roll-Forward Error Budget Impact

| Deploy type | 20% error rate on 1% canary | 20% error rate on 100% rollout |
|-------------|-----------------------------|-------------------------------|
| Budget consumed per minute | 0.002% | 0.2% |
| ~100× less impact on canary | ✓ | — |

---

## Toil Identification Checklist

Toil when ≥3 of these are true:
- [ ] Manual (human action, not just human judgment)
- [ ] Repetitive (occurs more than once)
- [ ] Automatable (runbook reads like pseudocode)
- [ ] Nontactical/reactive (interrupts higher-value work)
- [ ] Lacks enduring value (resolving today doesn't prevent tomorrow)
- [ ] Scales with infrastructure size

---

## Error Budget Policy Decision Tree

```
Is error budget exhausted?
  No → releases proceed normally
  Yes → Was the cause internal (our code/process)?
    Yes → Halt releases + mandatory reliability work
    No (external dependency, infra incident, test traffic)→ May continue feature work
  Single incident >20% of budget? → Mandatory postmortem with P0 AI
```

---

## PromQL Alerting Template (99.9% SLO)

```yaml
- alert: SLOBurnRatePage
  expr: |
    (job:slo_errors_per_request:ratio_rate1h{job="X"} > 0.0144
     and job:slo_errors_per_request:ratio_rate5m{job="X"} > 0.0144)
    or
    (job:slo_errors_per_request:ratio_rate6h{job="X"} > 0.006
     and job:slo_errors_per_request:ratio_rate30m{job="X"} > 0.006)
  severity: page

- alert: SLOBurnRateTicket
  expr: |
    job:slo_errors_per_request:ratio_rate3d{job="X"} > 0.001
    and job:slo_errors_per_request:ratio_rate6h{job="X"} > 0.001
  severity: ticket
```
Replace `X` with job label. Adjust thresholds for non-99.9% SLOs using `burn_rate × (1 - SLO)`.
