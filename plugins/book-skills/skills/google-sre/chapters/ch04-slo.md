# Chapter 4: Service Level Objectives

## Core Idea
SLI → SLO → SLA is a hierarchy of increasing commitment. SLOs are the primary driver of SRE prioritization; they must be chosen carefully because they shape both engineering work and user expectations.

## Frameworks Introduced
- **SLI (Service Level Indicator)**: A carefully defined quantitative measure of service level. Must be a ratio or measurable metric.
- **SLO (Service Level Objective)**: Target value or range for an SLI. Form: `SLI ≤ target` or `lower_bound ≤ SLI ≤ upper_bound`.
- **SLA (Service Level Agreement)**: Contract with consequences (financial penalties, rebates). SRE rarely sets SLAs but must ensure SLOs within them are met.
- **SLI categories by service type**:
  - User-facing serving: availability, latency, throughput
  - Storage: latency, availability, durability
  - Big data/pipelines: throughput, end-to-end latency
  - All systems: correctness

## Reference Tables

| Service Type | Key SLIs |
|---|---|
| User-facing | Availability, Latency (p99), Throughput (QPS) |
| Storage | Latency, Availability, Durability |
| Big Data | Throughput, End-to-end latency |
| All | Correctness |

**Example multi-level SLO** (recommended pattern):
```
90% of Get RPCs complete in < 1 ms
99% of Get RPCs complete in < 10 ms
99.9% of Get RPCs complete in < 100 ms
```

**SLO target selection rules** (5 heuristics):
1. Don't pick a target based on current performance (locks in heroic effort)
2. Keep it simple (complex aggregations obscure changes)
3. Avoid absolutes ("always available" is unrealistic)
4. Have as few SLOs as possible (if you can't win a priority argument with it, drop it)
5. Perfection can wait (start loose, tighten over time)

## Key Concepts
- **Percentiles over averages**: Always use p50/p95/p99/p99.9 for latency. Averages mask long-tail behavior.
- **Tail latency amplification**: If p99 of one backend is 100ms, the p50 of a frontend calling 2 such backends is ~100ms. One backend's tail becomes another's median.
- **Distribution over mean**: "Most metrics are better thought of as distributions rather than averages."
- **Safety margin**: Run internal SLO tighter than published external SLO — gives room to respond before users notice.
- **Don't overachieve**: If actual performance far exceeds SLO, users depend on the excess. Deliberately stay near the SLO floor.
- **Client-side collection**: Server-side metrics miss client-observed latency (JS execution, network round trips). Instrument both.
- **SLO as a forcing function**: A good SLO forces engineering prioritization. If you can never win a priority debate citing an SLO, that SLO is useless — drop it.

## Mental Models
- "Start with what users care about, not what you can measure." Work backward from user need to SLI selection.
- Use percentiles because "the higher the variance in response times, the more the typical user experience is affected by long-tail behavior."
- The 4-step control loop: Monitor SLI → Compare to SLO → Decide action → Act. Without SLO, step 2 doesn't exist.
- "An error budget is just an SLO for meeting other SLOs!"

## Anti-patterns
- **Picking SLOs based on current performance**: Locks in technical debt as a permanent requirement.
- **Using averages for latency SLIs**: Hides tail behavior. The p99 of a 100-req/s service failing 1% of requests at 5s is invisible in the mean.
- **Too many SLOs**: Dilutes attention; if every metric is critical, none are.
- **SLO without consequences**: Without error budget enforcement, SLOs are aspirational decoration.
- **Over-achieving the SLO**: Creates false user dependency on performance you haven't committed to.

## Key Takeaways
1. SLI is what you measure; SLO is the target; SLA is the contractual consequence of missing it. Most people mean SLO when they say SLA.
2. Use percentiles for latency (p95/p99/p99.9); averages hide the worst-case experience that actually drives user churn.
3. Choosing the right SLO: start from what users care about, work backward to measurable proxies.
4. Keep a tighter internal SLO than the published external one — this is your operational safety margin.
5. Deliberately stay near the SLO floor ("don't overachieve") — Chubby's intentional outage is the canonical example.

## Connects To
- **Ch 3**: Error budget = 1 − SLO; the two chapters must be read together.
- **Ch 10**: Borgmon/Prometheus — the tooling for measuring and alerting on SLIs.
- **Ch 6**: Monitoring — the Four Golden Signals are the canonical SLIs for user-facing services.
