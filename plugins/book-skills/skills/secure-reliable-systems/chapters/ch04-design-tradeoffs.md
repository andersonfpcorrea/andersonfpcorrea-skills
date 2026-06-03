# Chapter 4: Design Tradeoffs

## Core Idea
Security and reliability are emergent properties of system design — not modules you can add — and must be treated as first-class requirements from the earliest design phase, because retrofitting them into an existing system is disproportionately expensive.

## Frameworks Introduced
- **Feature vs. Emergent Requirements**: Feature requirements (functional) have direct code, tests, and traceability. Security and reliability are *emergent* — they arise from the whole system's architecture, dependency structure, and operational practices, not from any single component.
- **Initial Velocity vs. Sustained Velocity**: Deferring security/reliability appears to accelerate early development but accumulates technical debt that increasingly taxes sustained velocity. The book frames this as the central tension of the design phase.
- **Google Design Document Template**: A formal template that includes sections for Scalability, Redundancy/Reliability, Dependency considerations, Data integrity, SLA requirements, and Security/privacy considerations — forcing these to be explicit at design time.

## Key Concepts
- **Nonfunctional requirements**: Requirements that apply to the whole system (security, reliability, performance) rather than specific features; often implicit or unstated until violated.
- **Critical requirements**: The subset of features without which there is no viable product.
- **"Can't bolt it on"**: Because security and reliability emerge from architecture, retrofitting requires fundamental refactors or partial rewrites — under time pressure, with extra risk.
- **Third-party dependency tradeoff**: Outsourcing payment processing (example) reduces data exposure risk but adds reliability dependency (third-party outage = your outage), may reintroduce storage risk if fallback queuing is needed.
- **Microservices + framework adoption**: Google adopted a common microservices framework that enforces authentication, authorization, logging, and rate limiting by default — removing per-service implementation decisions and eliminating whole classes of vulnerabilities.

## Reference Table: Emergent Properties — Sources
| Property | Arises From |
|---|---|
| Reliability | Component decomposition, dependency availability, load balancing, monitoring, testing integration |
| Security | Subcomponent decomposition + trust relationships, implementation language/platform, security reviews in SDLC, audit logging/anomaly detection |

## Mental Models
- "No amount of testing and tactical bug-fixing will change" a system with tangled dependencies and poorly defined interfaces — only design changes do.
- Treat security and reliability requirements like your most critical feature requirements: define them up front, review them with SRE and security teams, include them in the design document.

## Anti-patterns
- **Deferred security**: Treating security as a launch-blocking checklist item rather than an ongoing design constraint — leads to expensive retrofits and accumulated vulnerabilities.
- **Redundancy without reliability analysis**: Adding a payment fallback queue that reintroduces the very data-at-rest risks you outsourced to avoid.
- **Over-scoped changes**: Tying a security change to unrelated refactors — harder to understand, harder to roll back, harder to review.

## Key Takeaways
1. Treat security and reliability as emergent properties requiring architectural investment, not modules to install.
2. Early design reviews (Google's design document template) surface security/reliability implications before expensive commitments are made.
3. The real cost of deferring security/reliability is compounding: higher retrofit cost, technical debt tax, and exposure to incidents during the gap.
4. Adopting common frameworks for auth, logging, rate limiting eliminates per-service implementation variance and associated bug classes.
5. When weighing a third-party dependency, explicitly map all reliability failure modes it introduces alongside the security risk it removes.

## Connects To
- **Ch 5**: Least privilege as a design constraint, not a bolt-on
- **Ch 6**: Understandability — the design property that enables reasoning about security invariants
- **Ch 7**: Adapting design for the changing landscape
- **Ch 12**: Frameworks that enforce security/reliability at the code level
