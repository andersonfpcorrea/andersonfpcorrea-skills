# Chapter 3: Embracing Risk

## Core Idea
100% reliability is never the right target — reliability has diminishing returns and increasing costs. The error budget framework makes risk-taking explicit, objective, and shared between dev and SRE.

## Frameworks Introduced
- **Time-based availability formula**:
  ```
  availability = uptime / (uptime + downtime)
  ```
  Use for systems with clear up/down states. A 99.99% SLO = 52.56 minutes downtime/year.

- **Aggregate availability (Google's preferred metric)**:
  ```
  availability = successful_requests / total_requests
  ```
  Use for distributed systems that are partially up (Google is always serving *some* traffic).

- **Error Budget**:
  ```
  error_budget = 1 − SLO_target
  ```
  A 99.999% SLO → 0.001% error budget per quarter. Budget is spent on every failure (infra or push). When exhausted: halt releases.

- **Risk tolerance tiering for infrastructure**: Offer the same hardware at multiple service levels (low-latency vs. throughput clusters); expose cost explicitly so clients self-select the cheapest tier that meets their needs.

## Reference Tables

| Availability | Annual Downtime | Quarterly Downtime |
|---|---|---|
| 99% | 3.65 days | ~21.9 hours |
| 99.9% | 8.76 hours | ~2.19 hours |
| 99.99% | 52.56 minutes | ~13.14 minutes |
| 99.999% | 5.26 minutes | ~1.31 minutes |

| Factor | Consumer Service | Infrastructure Service |
|---|---|---|
| Ownership | Clear product manager | Multiple clients, no single owner |
| Risk tolerance | Determined by market position | Vary by use case (latency vs. throughput) |
| SLO setting | Revenue/user expectations | Publish explicit tiers; let clients choose |

## Key Concepts
- **Cost of reliability**: Non-linear — each additional nine costs ~100× the previous.
- **Opportunity cost**: Engineering time spent on reliability instead of features.
- **ISP background error rate**: 0.01%–1% — driving errors below this threshold yields no user-visible improvement.
- **Quarterly error budget cycle**: Set SLO → measure weekly → if budget near zero, slow releases; if exceeded, halt until budget replenishes.
- **Planned outages**: If a service never exceeds its SLO, it may be *too* reliable — clients build on current behavior and become fragile to any degradation (the Chubby lesson).

## Mental Models
- "We view the availability target as both a minimum and a maximum" — never consistently exceed SLO; you're wasting reliability on users who can't notice it.
- Error budget = "how much risk can we spend this quarter?" Dev and SRE both own this number.
- Cost/benefit for one more nine: `value = revenue × delta_availability`. If adding a nine costs more than this, don't add it.
- Synthesize outages if real failures don't consume enough budget (Chubby intentional outage pattern).

## Anti-patterns
- **Targeting 100% availability**: Impossible, expensive, and unnecessary for virtually any service.
- **Treating SLO purely as a floor**: If you always exceed it by 50×, clients depend on the excess, and you've lost the ability to manage the service.
- **Uniform infrastructure SLOs**: Different clients of the same infrastructure need different guarantees — partition into tiers rather than building one ultra-reliable (expensive) monolith.
- **Error budget politics**: If one team sets the SLO and the other measures it, expect conflict. Both must jointly agree on the metric.

## Key Takeaways
1. Every service has a right reliability target: high enough that users aren't impacted, no higher because each increment costs exponentially more.
2. Error budget = `1 − SLO`. It's the permitted unreliability. Spend it on risky launches; when exhausted, freeze launches.
3. Aggregate availability (request success rate) is more useful than uptime for distributed services.
4. Infrastructure services should publish explicit service tiers so clients self-select cost vs. reliability trade-offs.
5. The global Chubby planned outage shows that being too reliable creates fragile dependencies — intentionally take the system to its SLO floor.

## Connects To
- **Ch 1**: Error budget first introduced as the resolution to dev/ops conflict.
- **Ch 4**: SLOs in detail — how to pick the right SLI/SLO to define the error budget.
- **Ch 22**: Cascading failures — what happens when error budgets are violated by infrastructure failures.
