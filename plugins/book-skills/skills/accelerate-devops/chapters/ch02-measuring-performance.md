# Chapter 2: Measuring Performance

## Core Idea
The four DORA key metrics — deployment frequency, lead time for changes, MTTR, and change failure rate — are the only valid, globally-scoped measures of software delivery performance; high performers excel on all four simultaneously.

## Frameworks Introduced
- **The Four Key Metrics (DORA Metrics)**: Two measure tempo, two measure stability. High performers beat low performers on all four — proving speed and stability reinforce, not trade off against, each other.
  - **Deployment Frequency**: How often code is deployed to production. Proxy for batch size.
  - **Lead Time for Changes**: Time from code commit to successfully running in production. Delivery lead time (not design lead time).
  - **Mean Time to Restore (MTTR)**: Time to restore service after an incident.
  - **Change Failure Rate**: Percentage of deployments causing degraded service or requiring remediation.

- **Cluster Analysis for Performance Tiers**: Statistical clustering (no pre-imposed definitions) produces three natural tiers — high, medium, low performers — that are significantly different on all four metrics.

## Reference Tables

### Software Delivery Performance — 2016

| Metric | High | Medium | Low |
|---|---|---|---|
| Deployment Frequency | On demand (multiple/day) | Once/week – once/month | Once/month – once/6 months |
| Lead Time for Changes | < 1 hour | 1 week – 1 month | 1 month – 6 months |
| MTTR | < 1 hour | < 1 day | < 1 day* |
| Change Failure Rate | 0–15% | 31–45% | 16–30% |

### Software Delivery Performance — 2017

| Metric | High | Medium | Low |
|---|---|---|---|
| Deployment Frequency | On demand (multiple/day) | Once/week – once/month | Once/week – once/month* |
| Lead Time for Changes | < 1 hour | 1 week – 1 month | 1 week – 1 month* |
| MTTR | < 1 hour | < 1 day | 1 day – 1 week |
| Change Failure Rate | 0–15% | 0–15% | 31–45% |

*Low performers were statistically lower on average but had the same median as medium performers.

**2017 headline numbers (high vs. low):** 46× more frequent deployments, 440× faster lead time, 170× faster MTTR, 5× lower change failure rate.

## Key Concepts
- **Delivery lead time**: Time from code commit to code running in production; the measurable half of lead time (the "fuzzy front end" design phase is excluded).
- **Deployment frequency**: Proxy for batch size — higher frequency means smaller batches, which reduces risk and accelerates feedback.
- **Failure demand**: Work caused by failing to do the right thing the first time; continuous delivery reduces failure demand.
- **Global vs. local metrics**: Good metrics measure system-level outcomes that require cross-function collaboration, not local team outputs (lines of code, velocity, utilization).

## Mental Models
- Deployment frequency as batch size: the more often you deploy, the smaller each batch; smaller batches mean faster feedback and lower risk.
- Queue theory: as utilization approaches 100%, lead time approaches infinity. High utilization is an anti-metric.
- "High performers do better on all measures" — reject the speed-vs-stability tradeoff assumption.

## Anti-patterns
- **Lines of code as productivity**: Incentivizes bloat; the 10-line solution beats the 1,000-line solution.
- **Velocity as cross-team comparison**: Teams game it; it destroys its own utility and inhibits collaboration.
- **Utilization as productivity proxy**: Above a threshold, utilization increases lead times exponentially (queue theory).
- **Change Advisory Boards (CABs)**: Negatively correlated with tempo and stability; "approval by an external body is worse than having no change approval process at all."

## Key Takeaways
1. Measure global outcomes, not local outputs — four metrics covering tempo and stability are sufficient.
2. High performers prove that speed and stability are not a tradeoff; they reinforce each other when quality is built in.
3. The performance gap widens each year — organizations that don't improve fall further behind.
4. Use the 2017 table to benchmark your team; set explicit targets for all four metrics.

## Connects To
- **Ch 3**: Westrum culture — pathological cultures turn metrics into control rather than learning tools.
- **Ch 7**: Change approval processes and their negative effect on tempo/stability.
- **Appendix B**: Full statistical findings including organizational performance correlations.
