# Chapter 10: Architecting for Reliability

## Core Idea
SLOs should drive architecture decisions from the start, not be bolted on after the fact. Hardware choices, service decomposition, and failure mode anticipation all determine the maximum achievable SLO before a line of code is written.

## Framework: SLO-First Architecture

Design process:
1. Gather user requirements as user journeys (= SLIs)
2. Determine acceptable failure impact for each journey
3. Work backward from SLO requirements to component reliability requirements
4. Choose hardware, topology, and patterns based on what the SLO demands

Gordon Bell's principle: "The cheapest, fastest, and most reliable components are those that aren't there." Simplicity directly improves SLOs.

## Hardware vs. SLO Trade-offs

| Storage | Read latency | IOPS | SLI impact | Cost |
|---|---|---|---|---|
| Hard disk | 10 ms seek + 5–40 ms read | 100s | Baseline; high variance | $ |
| SSD | ~1 ms | 10,000 | 10× reduction; predictable | $$ |
| RAM | ~0.01 ms | 100,000 | 100× reduction | $$$ |

Hardware choice sets a hard ceiling on achievable latency SLOs. A 250 ms latency SLO cannot be met if disk seeks average 50 ms each.

## MTTR and Architecture

For a 99.9% SLO (three nines), the total allowable downtime per quarter is ~2.6 hours.

- **Bare metal failure replacement**: Procurement + install + setup + verification = hours to days. A single hardware failure can exhaust the entire quarterly error budget.
- **Cloud/containerized services**: Rescheduling time = seconds to minutes. MTTR bounded by scheduler SLO.

Conclusion: SLOs above ~99.9% over quarterly windows generally require containerized/cloud deployments to stay within MTTR budgets.

## Monolith vs. Microservices for SLOs

- **Monolith**: Single binary; easy to scale vertically but hard to isolate failures. One buggy path affects the entire service.
- **Microservices**: Independent scaling and deployment per component; failure isolation; enables per-component SLOs.
- **Service-oriented architecture** (pragmatic middle): Well-scoped services with RPC interfaces, scalable horizontally and vertically.

For SLO-driven design, service orientation allows:
- Independent error budget tracking per component
- Targeted reliability investments where SLOs are closest to exhaustion

## Three Types of Requests (for capacity planning)

The book models requests by their resource profile to anticipate failure modes:
1. **Latency-sensitive, CPU-light**: Needs fast response; scale horizontally for availability.
2. **CPU-heavy, latency-tolerant**: Needs capacity headroom; monitor utilization against latency SLO.
3. **Storage-bound**: Needs durable, low-latency storage; hardware choice is the primary SLO determinant.

## Quantitative Analysis of System Composition

Each added component multiplies unreliability. For a system with N components each at reliability r:
```
system_reliability = r^N
```
Use this to determine whether proposed architectures can meet the target SLO before building.

## SLOs as Architectural Requirements

- Write SLI requirements alongside functional requirements in system specs.
- Each architectural decision (hardware tier, data center count, replication factor) maps to a component reliability target.
- "Hope is not a strategy" — document the reliability ceiling each architectural choice imposes.

## Key Takeaways

1. Hardware choice sets a hard floor on latency and availability SLOs — decide early.
2. MTTR for bare-metal failures can exhaust error budgets; cloud/containerized architectures bound MTTR to seconds.
3. Compound reliability: every added component multiplies unreliability; minimize component count where possible.
4. SLOs belong in system specifications alongside functional requirements.
5. Measure user journeys from the beginning, not after the architecture is locked.

## Connects To

- **Ch 4**: Dependency math (compound reliability)
- **Ch 9**: Probability tools for quantitative system analysis
- **Ch 11**: Data reliability architecture special cases
