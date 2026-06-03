# Chapter 5: Architecture

## Core Idea
High performance is achievable with any type of system (including mainframes and legacy) provided teams and systems are loosely coupled — and loose coupling enables productivity to scale linearly or better with headcount.

## Frameworks Introduced
- **Two Critical Architectural Properties**:
  1. **Testability**: Teams can do most testing without requiring an integrated environment.
  2. **Deployability**: Teams can deploy/release their application independently of services it depends on.

  These two properties predict high-performer status better than system type (greenfield vs. mainframe, microservices vs. monolith).

- **Inverse Conway Maneuver**: Deliberately evolve team and organizational structure to achieve the desired architecture. Architecture and teams must both be loosely coupled. "Organizations which design systems are constrained to produce designs which are copies of their communication structures" (Conway 1968) — so you must change the org structure to change the architecture.

- **Loosely Coupled Architecture Checklist**: Teams that score high can:
  - Make large-scale design changes without permission from outside the team.
  - Change their system without depending on or creating work for other teams.
  - Deploy independently of services they depend on.
  - Do most testing without an integrated environment.
  - Deploy during normal business hours with negligible downtime.

## Key Concepts
- **Loose coupling**: The architectural property enabling teams to test, deploy, and change their components without coordination with other teams.
- **Deployability/testability**: The two architectural dimensions that matter — more predictive of performance than the specific technology or system type used.
- **Scaling law**: For teams deploying at least once/day — high performers deploy at increasing frequency per developer as team grows; low performers at decreasing frequency. Loosely coupled architecture is the mechanism.
- **Conway's Law**: Org communication structure mirrors system architecture; to get a loosely coupled architecture, you need loosely coupled teams.

## Mental Models
- Microservices or containers don't automatically give you loose coupling — many "service-oriented architectures" fail because services cannot be tested or deployed independently. The label doesn't matter; the properties do.
- Tool standardization is not the same as architectural constraints: standardize infrastructure platforms and security toolchains, but let teams choose their application tools.

## Anti-patterns
- **Tool mandate without user need**: Approved-list tool policies that prevent teams from choosing the right tool for the job correlate with lower performance. Allow tool choice unless there's a specific operational reason not to.
- **Custom software outsourcing as core capability**: Low performers were more likely to depend on custom software developed by another company — outsourcing strategic software delivery degrades performance.
- **Architecture review as governance theater**: Architects focused on technology choices rather than on enabling engineers to achieve better outcomes.

## Key Takeaways
1. System type (mainframe, greenfield, COTS, SaaS) is NOT significantly correlated with delivery performance — architecture properties are.
2. The biggest single contributor to CD capability (2017 data) was loosely coupled, well-encapsulated architecture — bigger than test or deployment automation.
3. Loosely coupled architecture enables delivery teams to complete work without requiring high-bandwidth coordination with other teams.
4. Cross-functional delivery teams (all skills from design through deploy/operate on one team) are required to achieve architectural loose coupling in practice.
5. High-performer scaling: as developers are added, deploy frequency per developer increases; for low performers it decreases.

## Connects To
- **Ch 4**: Architecture is a prerequisite for effective CD; the two capabilities reinforce each other.
- **Ch 7**: Lean management and WIP limits work better when architecture enables independent team work.
- **Appendix A**: Architecture capabilities 9–10 (loosely coupled architecture, architect for empowered teams).
