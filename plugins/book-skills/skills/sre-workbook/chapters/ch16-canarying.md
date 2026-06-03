# Chapter 16: Canarying Releases

## Core Idea
Canarying is partial, time-limited deployment of a change with evaluation — the only reliable way to catch defects that only manifest with real production traffic. Most incidents are caused by releases; canaries limit their blast radius.

## Frameworks Introduced

### Release Engineering Principles
1. **Reproducible builds**: same inputs → same artifact every time.
2. **Automated builds**: CI produces artifacts automatically on commit.
3. **Automated tests**: artifacts are validated before deployment.
4. **Automated deployments**: computers, not humans, run deployments.
5. **Small deployments**: each release contains few changes (easier rollback; faster bug isolation).

### Canary Deployment Requirements
1. A method to route a subset of production traffic to the canary version.
2. An evaluation process that classifies the canary as "good" or "bad."
3. Integration of the canary evaluation into the release pipeline (not a manual step).

### Canary vs. Control
- **Canary**: the subset running the new version (small fraction of production)
- **Control**: the majority running the current stable version
- Always compare canary metrics against control metrics, not against a historical baseline (before/after evaluation is risky — external factors confound results).

## Canary Implementation Process

```
1. Deploy new version to canary pool (e.g., 1-5% of instances/traffic)
2. Run canary for defined duration (minimum: enough traffic for statistical significance)
3. Compare canary vs. control on key metrics:
   - Error rate (must be ≤ control)
   - Latency distribution (must be ≤ control at same percentiles)
   - Resource usage (CPU, memory — unexpected increases signal memory leaks)
   - Business metrics (conversion rate, user engagement if measurable)
4a. If canary is "good" → promote to full rollout (gradual: 5% → 25% → 100%)
4b. If canary is "bad" → drain canary traffic back to control; rollback
```

## Metric Selection for Canary Evaluation

| Metric quality | Criteria |
|---------------|----------|
| Indicates problems | Metrics that change detectably when something is wrong |
| Representative | Covers the traffic types the canary actually receives |
| Attributable | Changes in the metric are attributable to the canary, not external factors |
| Sensitive enough | Statistical power to detect the expected defect size in the canary window |

**Risks to avoid**:
- Before/after comparison: compares canary against historical baseline; seasonal/external changes confound results.
- Evaluate canary and control with identical metrics at identical times (A/B, not A vs. historical A).

## Gradual Canary (Better Metric Selection)
Deploy at 1% → wait → evaluate → 5% → wait → evaluate → etc. Gradual ramp improves metric signal quality by allowing more traffic to accumulate before each evaluation decision.

## Error Budget Impact: Canary vs. Roll-Forward
| Scenario | Error budget consumed |
|----------|-----------------------|
| Roll-forward (deploy to 100%, defect at 20% error rate) | Minutes × 20% × budget rate |
| Canary at 1% (same defect) | Minutes × 0.2% × budget rate (100× less) |

A 1% canary reduces error-budget impact of a bad deploy by ~100×.

## PromQL: Comparing Canary vs. Control Error Rates

```yaml
# Error rate by version label (canary vs. stable)
sum(rate(http_requests_total{status=~"5.."}[5m])) by (version)
/
sum(rate(http_requests_total[5m])) by (version)

# Latency p99 by version
histogram_quantile(0.99,
  sum(rate(http_request_duration_seconds_bucket[5m])) by (version, le)
)
```

## Canary for Non-Interactive Systems
- Batch jobs: run canary and control on split input datasets; compare output quality.
- Config changes: deploy config canary before full rollout (same as binary canary).
- ML models: shadow mode (run new model in parallel, compare outputs without serving them) before canary.

## Related Concepts
| Concept | Relation to Canarying |
|---------|----------------------|
| Blue/green deployment | Two full environments; instant switch; expensive (2× capacity); no gradual ramp |
| Traffic teeing | Duplicate traffic to canary; canary responses discarded; tests without user impact |
| Feature flags | Separate feature launch from binary release; enables instant rollback of a feature |

## Anti-patterns
- **Before/after comparison**: external factors invalidate the comparison.
- **Canary of one instance**: too small for statistical significance; single-instance quirks dominate signal.
- **Unlimited canary duration**: a canary that never graduates is not a canary, it's a shadow service.
- **No rollback plan**: evaluating without knowing how to roll back if the canary is bad.

## Key Takeaways
1. A 1% canary limits error budget impact of a bad release by ~100× vs. a full rollout.
2. Always compare canary vs. control, not canary vs. historical baseline.
3. Automate the evaluation decision; manual evaluation creates toil and delays.
4. Config changes need canarying too — not just binary deployments.

## Connects To
- **Ch 2**: Error budget is what canarying protects
- **Ch 5**: The SLO alerting system will page faster when the canary is small and isolated
- **Ch 14/15**: Config canaries apply the same principles to configuration changes
