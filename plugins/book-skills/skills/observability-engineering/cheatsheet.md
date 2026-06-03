# Observability Engineering — Quick Reference

## The Core Definition
> "If you can understand any state of your software system, no matter how novel or bizarre, by arbitrarily slicing and dicing high-cardinality and high-dimensionality telemetry data into any view you need, and use the core analysis loop to comparatively debug and quickly isolate the correct source of issues, without being required to define or predict those debugging needs in advance, then you have observability."

## Observability vs. Monitoring Decision

| Question | Use Monitoring | Use Observability |
|----------|---------------|------------------|
| What layer? | Infrastructure/systems | Application/code |
| What changes? | Monthly (packages) | Daily (commits) |
| Failure mode type | Known-unknowns | Unknown-unknowns |
| Alerting approach | Threshold on cause | SLO on symptom |
| Who uses it? | Ops team | Software engineers |

## The Real Three Pillars
1. **High cardinality** — UUID-level uniqueness in fields
2. **High dimensionality** — Hundreds of fields per event
3. **Explorability** — Ad hoc questions without predicting them

## Alert Quality Test
An alert MUST pass BOTH criteria or it should be deleted:
1. Reliable indicator that **user experience is degraded**
2. **Actionable** — systematic (not rote) way to investigate and fix

## SLO Burn Alert Rules
- Use **30-day sliding window** (not fixed calendar)
- Lookahead:baseline ratio: **4:1 maximum**
  - 24h lookahead → 6h baseline
  - 4h lookahead → 1h baseline
- Configure **both** short and long lookahead windows
- **Event-based SLIs** over time-based (request = good/bad, not minute = good/bad)

## Sampling Quick Reference
| Strategy | When to use |
|----------|------------|
| Fixed-rate | Simple, predictable traffic |
| Target-rate (dynamic) | Variable traffic volume |
| Per-key | Need higher rate for errors vs. successes |
| Head-based | Sampling criteria known at request start |
| Tail-based (buffered) | Criteria depend on outcome (latency, error) |

Always: record `sampleRate` in every sampled event.

## OTel Instrumentation Checklist
- [ ] Auto-instrument HTTP/gRPC frameworks first
- [ ] Add custom spans for expensive/critical code paths
- [ ] Add business attributes: user ID, customer tier, feature flags, cart value
- [ ] Export via OTLP (not vendor-specific exporter)
- [ ] Ask for every PR: "How will I know if this change is working as intended?"

## Data Store Requirements (for building)
1. Query results in **seconds** (p50 <100ms, p99 <10s)
2. **Any field queryable** ad hoc — no pre-declared indexes
3. Data **available within seconds** of ingestion
4. **Time** is the only privileged dimension
5. Fault-tolerant and durable

TSDBs: INCOMPATIBLE (cardinality explosion). Use columnar store with time-partitioned segments.

## Build vs. Buy Decision
| Factor | Favors Build | Favors Buy |
|--------|-------------|-----------|
| Core business | Observability vendor | Any other business |
| Org maturity | Strong PM + eng bandwidth | Lean, fast-moving |
| Time to value | Long term OK | Need it now |
| Budget visibility | Budget for engineering > software | Can justify software spend |

**Default recommendation**: Buy + build internal integration team.

## Observability Maturity Model (OMM) — 5 Capabilities
1. Respond to System Failure with Resilience (MTTR, alert quality)
2. Deliver High-Quality Code (production confidence)
3. Manage Complexity and Technical Debt (forward progress ratio)
4. Release on a Predictable Cadence (deploy frequency, feature flags)
5. Understand User Behavior (product-market fit, KPI access)

## Signs You Have "Enough" Observability
- Teams add instrumentation instinctively with every code change
- Code reviews check telemetry quality
- Non-engineering teams self-serve data requests
- "Mystery" incidents are decreasing
- TTD and TTR improving across the organization
