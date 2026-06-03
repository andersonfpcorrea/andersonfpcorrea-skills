# Chapter 9: How Observability and Monitoring Come Together

## Core Idea
Monitoring and observability are complementary, not competing. Monitoring is best for **system-level** (infrastructure) concerns; observability is best for **software-level** (application code) concerns. The split is determined by operational responsibility, not where infrastructure lives.

## Frameworks Introduced
- **Systems vs. software split**:
  - **System**: Infrastructure you are responsible for operating — databases, containers, VMs, message queues you manage yourself. Changes monthly. Monitoring is the right tool.
  - **Software**: Code you actively develop that delivers customer value. Changes daily. Observability is the right tool.
  - Decision rule: The more infrastructure you outsource (IaaS → PaaS → SaaS), the less traditional monitoring you need.

## Reference Tables
| Factor | Your Systems | Your Software |
|--------|-------------|---------------|
| Rate of change | Package updates (monthly) | Repo commits (daily) |
| Predictability | High (stable) | Low (many new features) |
| Value to business | Low (cost center) | High (revenue generator) |
| Core concern | Is the service healthy? | Can each request acquire resources for end-to-end execution? |
| Method for understanding | Monitoring | Observability |

## Key Concepts
- **Known-unknowns**: What monitoring detects — failure modes you know can occur, checked against thresholds.
- **Unknown-unknowns**: What observability detects — novel failures you didn't predict.
- **Higher-order infrastructure metrics**: CPU, memory, disk — still relevant to software engineers as early warning signals of code-caused problems. Exception to the neat systems/software split.
- **Correlation, not replacement**: When performance issues occur, monitoring quickly rules in/out infrastructure causes; observability then explains application behavior.

## Mental Models
- Think of monitoring and observability as a diagnostic toolkit: monitoring is the thermometer (is the system running hot?), observability is the MRI (what exactly is wrong and where?).
- The appropriate mix shifts with infrastructure responsibility: more infrastructure ownership → more monitoring; more outsourced infrastructure → more observability.
- Aggregate metrics work perfectly for infrastructure warning signals; they fail for individual request diagnosis.

## Anti-patterns
- **Throwing out all monitoring when adopting observability**: Dangerous for organizations with infrastructure responsibility. Monitoring remains the right tool for system-level concerns.
- **Using metrics to understand application-level user behavior**: Coarse aggregates cannot decompose to individual requests; customer experience requires observability.
- **Conflating monitoring and observability tools**: They serve different purposes; forcing one to do both creates friction.

## Key Takeaways
1. The guiding principle: observability owns application health; monitoring owns infrastructure health.
2. The more you outsource infrastructure (Heroku, Lambda, managed DBs), the less traditional monitoring you need.
3. Higher-order infrastructure metrics (CPU, memory, disk) are the exception — software engineers should watch these as code-change signals.
4. For correlation during incidents, seeing system metrics alongside application observability data in one view is the ideal.
5. Legacy stable services need not be ripped out; add observability for new services and leave monitoring for unchanged stable infrastructure.

## Connects To
- **Ch 1**: Establishes monitoring for known-unknowns vs. observability for unknown-unknowns.
- **Ch 12**: SLOs bridge the two worlds — symptom-based alerting uses application observability to answer "is the user experience degraded?"
- **Ch 19**: The business case for observability must account for existing monitoring investments.
