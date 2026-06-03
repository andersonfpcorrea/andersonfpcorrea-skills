# Chapter 2: Implementing SLOs

## Core Idea
Step-by-step recipe for setting up initial SLOs, error budgets, and the organizational agreement needed to use them as decision-making tools — not just reporting metrics.

## Frameworks Introduced
- **SLI as ratio**: SLI = good events / total events (ranges 0–100%). Error budget = 100% − SLO. This form enables consistent tooling (alerting, reporting, dashboards) across all SLIs.
- **SLI specification vs. SLI implementation**: Specification = what outcome matters to users, independent of measurement method. Implementation = specification + a specific way to measure it. One spec can have multiple implementations with different quality/coverage/cost tradeoffs.
- **Component type → SLI catalog**:
  - Request-driven: availability (% successful responses), latency (% requests < threshold), quality (% undegraded responses)
  - Pipeline: freshness (% records updated recently), correctness (% records with correct output), coverage (% jobs processing target data volume)
  - Storage: durability (% records written that can be read)
- **Starter SLO recipe** (4 prerequisites): stakeholder-approved SLOs; engineering team agrees they're achievable; org commits to error-budget-based decisions; process in place to refine SLOs.

## Key Concepts
- **SLO**: target level of reliability; above it, users are happy; below it, users complain or churn
- **SLA**: business contract; compensation mechanism when users are very unhappy (distinct from SLO)
- **Error budget**: 100% − SLO; quantifies how much unreliability is acceptable; drives prioritization
- **Error budget policy**: formal document committing the org to halt feature work when budget is exhausted (see Appendix B)
- **100% is wrong**: no marginal utility past ~99.9X% for most users; only keeps you reactive; forbids change
- **Time window**: rolling 28-day (or 4-week) window preferred; calendar quarter works for business alignment

## Reference Table: SLI Types by Component

| Component | SLI Type | Description |
|-----------|----------|-------------|
| Request-driven | Availability | % requests with successful response (non-5XX) |
| Request-driven | Latency | % requests faster than threshold |
| Request-driven | Quality | % responses served in undegraded state |
| Pipeline | Freshness | % records updated within time threshold |
| Pipeline | Correctness | % records with correct output |
| Pipeline | Coverage | % jobs processing ≥ target data volume |
| Storage | Durability | % written records successfully readable |

## Worked Example: Mobile Game Service
**Architecture**: phone app → HTTP API → state store; pipeline → league table store; avatar uploads.

**SLIs chosen**:
- API availability: `count(non-5XX api requests) / count(all api requests)`
- API latency: `count(api requests < 400ms) / count(all api requests)` AND `count(api requests < 850ms) / count(all api requests)`
- Pipeline freshness: `count(data requests with freshness < 1min) / count(all data requests)`
- Pipeline correctness: prober injects known data; `count(correct outputs) / count(all prober requests)`

**SLOs set** (from Appendix A):
- API availability: 97%
- API latency: 90% < 400ms, 99% < 850ms
- HTTP server availability: 99%; latency: 90% < 200ms, 99% < 1000ms
- Pipeline freshness: 90% < 1min, 99% < 10min
- Pipeline correctness: 99.99999%

**Error budget formula**:
```
budget (errors) = total_requests × (1 - SLO_threshold)
Example: 1,000,000 requests × 0.03 = 30,000 allowed errors for 97% availability SLO
```

## Key Takeaways
1. SLI = good events / total events (0–100%); this form makes all tooling uniform.
2. Start with availability and latency; add freshness/correctness/durability as you mature.
3. Get four things before calling it a real SLO: stakeholder approval, engineering agreement on achievability, commitment to use it for decisions, and a refinement process.
4. Use multiple latency thresholds (e.g., 90th and 99th percentile) to capture typical and tail experience.
5. Use the error budget for prioritization — "which project saves more budget?" is the decision framework.

## Connects To
- **Ch 5**: How to alert on SLO burn rate (consumes the error budget from this chapter)
- **Ch 3**: Real-world SLO adoption case studies (Evernote, Home Depot)
- **Appendix A**: Complete example SLO document for the mobile game
- **Appendix B**: Complete error budget policy template
