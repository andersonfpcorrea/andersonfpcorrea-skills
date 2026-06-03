# Patterns — The Site Reliability Workbook

## SLO Implementation Pattern

**When to use**: starting SLO program or improving an existing one.
**How**:
1. Choose component type (request-driven, pipeline, storage) → map to SLI types.
2. Write SLI specification: "% of requests returning < 400ms, measured from load balancer."
3. Choose cheapest implementation that provides adequate accuracy.
4. Calculate error budget: `budget = total_requests × (1 - SLO_target)`.
5. Get stakeholder sign-off, establish error budget policy.
6. Iterate using 4-week rolling window.

**Trade-offs**: starting with conservative targets avoids committing to an unrealistic SLO; current performance is a safe baseline.

---

## Multiwindow Multi-Burn-Rate Alerting Pattern

**When to use**: all production services with SLOs.
**How** (for 99.9% SLO, 30-day window):
```
Page: long=1h, short=5m, burn=14.4, budget_consumed=2%
Page: long=6h, short=30m, burn=6,   budget_consumed=5%
Ticket: long=3d, short=6h, burn=1,  budget_consumed=10%
```
Error threshold per alert = `burn_rate × (1 - SLO)`.
Short window = 1/12 of long window.
**Trade-offs**: requires more parameters; scales by grouping similar services into 5 request classes.

---

## Error Budget Policy Pattern

**When to use**: any service with an SLO.
**How**:
- Policy triggers: error budget exhausted for the rolling window.
- Mandatory action: halt all non-P0 releases until back within SLO.
- If cause was own code/process: mandatory reliability work.
- If cause was external: team may continue feature work.
- Single incident >20% of budget: mandatory postmortem with P0 AI.
- Escalation: disagreements go to CTO/VP.

**Trade-offs**: requires buy-in from product management and leadership before any incident; don't write it during an outage.

---

## Incident Command Pattern (IMAG/ICS)

**When to use**: any incident requiring more than one person.
**How**:
- First person declares incident, becomes default IC.
- IC delegates OL (debugging/mitigation) and CL (communications) as needed.
- CL opens war room channel; posts status updates every 15–30 minutes.
- OL keeps working document updated in real time.
- IC stays in 3Cs (Coordinate, Communicate, Control) — does not debug.
- At resolution: IC assigns postmortem owner; documents timeline.

**Trade-offs**: overhead for trivial incidents; but declaring early costs little; not declaring costs a lot.

---

## Blameless Postmortem Pattern

**When to use**: any significant incident or near-miss.
**How**:
1. Assign owner (single person); target first draft within 24h.
2. Fill in: summary, timeline, root causes (systemic), trigger, recovery, action items.
3. Action items must have: owner, tracking bug, specific description, mixed priorities, preventative items.
4. Share widely: announce to relevant mailing lists, not just team.
5. Review action items at next team meeting; track in bug system.

**Trade-offs**: requires blameless culture as prerequisite; leadership must model the behavior.

---

## Canary Deployment Pattern

**When to use**: all binary and config changes in production.
**How**:
1. Deploy new version to 1–5% of traffic/instances.
2. Compare canary vs. control (not canary vs. historical): error rate, latency, resource usage.
3. Minimum canary duration: enough traffic for statistical significance.
4. If good: gradually promote (5% → 25% → 100%).
5. If bad: drain traffic back to control; rollback.

**Trade-offs**: requires traffic-splitting infrastructure; worth it even for "simple" changes — most incidents are caused by releases.

---

## NALSD Iterative Design Pattern

**When to use**: designing any new production system.
**How**:
1. State requirements as concrete numbers (QPS, bytes, latency budget).
2. Design for one machine: can it handle the load? What's the bottleneck?
3. Add the minimum distribution to fix the bottleneck.
4. Evaluate failure modes for the new design.
5. Repeat from step 2 with the new bottleneck.

**Trade-offs**: takes more upfront time; produces cost-aware, failure-aware designs that don't require expensive rework post-launch.

---

## Toil Elimination Pattern

**When to use**: any operational task that meets ≥3 toil characteristics (manual, repetitive, automatable, reactive, no enduring value, scales with system).
**How**:
1. Measure current toil in hours/week (or tickets/week, patches/week).
2. Calculate ROI: direct time savings + indirect benefits (morale, fewer outages, security).
3. Fix root cause, not symptom — removing the incentive to fix root cause is worse than not automating.
4. Track continuously: before, during, after.

**Trade-offs**: automation requires investment; toil that generates <15 minutes/week may not be worth automating; indirect benefits usually dominate the ROI calculation.

---

## SLI for Batch Pipelines Pattern

**When to use**: any data processing pipeline.
**How** (VALET applied to pipelines):
- **Freshness**: `count(reads with data < threshold age) / count(all reads)` — measured at consumers, not at the pipeline itself.
- **Correctness**: inject synthetic data with known output; `count(correct outputs) / count(prober requests)`.
- **Coverage**: `count(pipeline runs processing 100% of records) / count(all runs)`.
- Track freshness at consumers, not at pipeline output — user experience is what matters.

**Trade-offs**: correctness probers require maintaining synthetic test data; but they are the only reliable signal for silent data corruption.

---

## Configuration Safety Pattern

**When to use**: any configuration that will be applied to production systems.
**How**:
1. Store config in source control with code review.
2. Use a hermetic DSL (Jsonnet, Dhall) — not YAML+Jinja.
3. Validate at CI time (before merge, not at runtime).
4. Apply gradually (canary, not all-at-once).
5. Never interleave evaluation with side effects.

**Trade-offs**: hermetic DSLs require learning curve; pays off at scale when config changes cause fewer incidents.

---

## Request Class Bucketing Pattern (Alerting at Scale)

**When to use**: services with multiple request types; avoiding per-endpoint custom alerting parameters.
**How**:
```
CRITICAL:  99.99% availability, 100ms p90, 200ms p99
HIGH_FAST: 99.9%  availability, 100ms p90, 200ms p99
HIGH_SLOW: 99.9%  availability, 1s p90,   5s p99
LOW:       99%    availability, no latency SLO
NO_SLO:    no SLO
```
Assign each endpoint to a bucket; apply shared alerting parameters for the bucket.

**Trade-offs**: less precise than per-endpoint SLOs; dramatically reduces alerting toil and cognitive load.
