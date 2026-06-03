# Chapter 27: Reliable Product Launches at Scale

## Core Idea
Launch Coordination Engineering (LCE) applies systematic SRE expertise to the unique challenge of product launches — a different skill set from day-to-day operations, requiring breadth across all Google systems, communication skills, and a curated launch checklist.

## Frameworks Introduced
- **Launch Coordination Engineering (LCE)**: A dedicated consulting team within SRE that audits products for reliability compliance, acts as technical liaison, drives launch momentum, and signs off on "safe" launches.
  - Advantages: breadth across Google systems, deep launch playbook accumulated from hundreds of prior launches.

- **Launch checklist categories** (the systematic approach to reliable launches):
  1. Architecture and dependencies
  2. Integration points with other services
  3. Capacity and resource requirements
  4. Failure mode analysis (graceful degradation)
  5. Monitoring and alerting readiness
  6. Rollout plan and rollback procedure
  7. Traffic ramp-up plan
  8. On-call staffing and escalation paths

## Key Concepts
- **Google launch definition**: Any new code that introduces an externally visible change. Up to 70 launches per week.
- **The NORAD Santa example**: Christmas Eve traffic spike = 25× normal peak = 1M+ req/s. "Make-children-cry switches" (kill switches to protect infrastructure) prepared in advance. Shows that even "whimsical" launches need rigorous preparation.
- **SRE engagement model for launches**: LCE vs. embedded SRE vs. no SRE involvement — governed by service complexity, traffic scale, and risk.
- **Gradual rollout strategy**: Ramp traffic incrementally (1% → 10% → 50% → 100%) with monitoring gates at each step. Abort criteria defined before launch.
- **Launch checklist value**: Accumulates organizational knowledge across hundreds of launches. Prevents each team from rediscovering the same mistakes. Especially valuable for "simple" launches that don't seem risky but have hidden dependencies.
- **LCE as educator**: Developer teams may be expert in their code but unfamiliar with cross-service dependencies, capacity planning, monitoring, and graceful degradation. LCE bridges this gap.
- **Product Readiness Review (PRR)**: Formal pre-launch review for high-risk launches. Covers architecture, capacity, monitoring, and operational readiness.

## Mental Models
- "Anticipating the many different ways this launch could go wrong" — launch preparation is systematic failure mode analysis, not optimism.
- LCE breadth across Google > depth in any single service. This breadth is the value.
- A checklist that accumulates from 100 launches is worth more than any individual's experience.

## Anti-patterns
- **Underestimating "simple" launches**: Hidden dependencies make "small" launches dangerous. Santa's satellite imagery wasn't "complicated" — until 1M kids hit it simultaneously.
- **No gradual rollout**: Big-bang launches have no abort mechanism when problems appear.
- **No defined rollback**: Always have a rollback plan with pre-defined criteria for triggering it.
- **Monitoring as afterthought**: If you can't observe the launch's health in real time, you can't respond to problems quickly enough.

## Key Takeaways
1. Even seemingly simple launches need preparation — traffic spikes, hidden dependencies, and missing monitoring cause unexpected incidents.
2. The launch checklist is an organizational asset — it prevents each team from rediscovering the same lessons.
3. Gradual rollout (1% → 10% → 100%) with monitoring gates at each stage is the standard approach.
4. LCE's value is breadth across the entire production system — depth in any specific service is secondary.
5. Define rollback criteria and abort conditions before the launch, not during the incident.

## Connects To
- **Ch 8**: Release engineering — hermetic builds and deployment automation underpin every launch.
- **Ch 32**: SRE engagement model — LCE is one model of SRE engagement with product teams.
- **Ch 6**: Monitoring — launch readiness includes monitoring and alerting being in place.
