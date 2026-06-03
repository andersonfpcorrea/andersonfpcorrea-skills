# Chapter 32: The Evolving SRE Engagement Model

## Core Idea
SRE engagement evolves from reactive onboarding (PRR model) to proactive early engagement to platform-based prevention. Earlier engagement = cheaper fixes + higher reliability "out of the gate."

## Frameworks Introduced
- **Production Readiness Review (PRR)**: The classic SRE onboarding gate. A structured review of a service's reliability needs before SRE accepts production responsibility. Covers architecture, monitoring, capacity, emergency response, change management, and performance.

- **Three SRE Engagement Models** (ascending maturity):
  1. **Simple PRR**: Review before launch; SRE takes on service; ongoing support. Reactive — service arrives at SRE's door.
  2. **Early Engagement**: SRE engaged during design phase. "Just as in software engineering — the earlier the bug is found, the cheaper it is to fix."
  3. **Frameworks and SRE Platform**: Product teams build on SRE-validated infrastructure. Short-circuits the entire onboarding problem by providing reliability-by-default building blocks.

- **Alternative support spectrum** (when full SRE engagement isn't warranted):
  - Documentation (Production Guide)
  - Consultation (a few hours; LCE-style)
  - Embedded SRE (operational overload intervention — see Ch 30)
  - Full SRE engagement (ongoing production responsibility)

## Key Concepts
- **PRR components** (what SRE reviews before accepting a service):
  - System architecture and interservice dependencies
  - Instrumentation, metrics, and monitoring
  - Emergency response procedures
  - Capacity planning
  - Change management
  - Performance: availability, latency, efficiency
- **Service lifecycle stages**: Design → Development → Pre-launch → Launch → Operations → Deprecation. SRE engagement earlier in this lifecycle produces better outcomes at lower cost.
- **SRE platform model**: The highest-leverage engagement — productize reliability best practices into infrastructure that any team can use without SRE involvement.
- **Not all services warrant SRE**: Design intentionally — services that don't need high reliability, or services that can use documentation/consultation, should not consume scarce SRE bandwidth.
- **Demand exceeds supply**: By design, development teams requesting SRE support exceed SRE capacity. SRE must triage and prioritize engagements.

## Mental Models
- "Earlier in the service lifecycle, the less time to onboard and the more reliable the service is from the start."
- Platform > Early Engagement > PRR > Documentation, in terms of scalability of SRE impact.
- The PRR is a gate, not a rubber stamp — SRE accepts services it can actually make reliable.

## Anti-patterns
- **Reactive-only onboarding**: PRRs when services arrive at SRE's door, not during design. Fixes structural reliability problems after they're baked in.
- **No alternative support for non-SRE services**: Services without SRE support also need reliability guidance — documentation and consultation scale further.
- **Treating all services as equal**: SRE bandwidth must be prioritized for highest-value, highest-risk services.

## Key Takeaways
1. PRR is the gate: SRE only accepts services it can improve. Architecture, monitoring, capacity, emergency response, change management, performance — all must be assessed.
2. Earlier engagement = cheaper fixes. SRE in the design phase prevents the need for expensive post-launch reliability work.
3. SRE platform is the highest-leverage model — reliability-by-default infrastructure that scales to all teams.
4. Not every service needs full SRE engagement; documentation and consultation are legitimate alternatives that scale.
5. By design, demand exceeds supply — prioritize SRE engagement for highest-impact services.

## Connects To
- **Ch 27**: Reliable launches — LCE is the consultation model for launches.
- **Ch 30**: Embedding an SRE — recovery from operational overload is an engagement variant.
- **Ch 1**: Introduction — SRE tenets (error budgets, 50% cap) are the standards PRR validates against.
