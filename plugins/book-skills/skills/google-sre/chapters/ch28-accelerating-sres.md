# Chapter 28: Accelerating SREs to On-Call and Beyond

## Core Idea
Investing in structured SRE onboarding builds trust (which enables on-call effectiveness) faster than "trial by fire." The Wheel of Misfortune is the flagship training technique.

## Frameworks Introduced
- **Wheel of Misfortune**: New SREs reenact a real historical postmortem with roles assigned (incident commander, on-call, etc.), the original incident commander present. Combines learning from past failures with real incident management practice.
  - When to use: Just before or shortly after a new SRE goes on-call.
  - How: Select a postmortem → assign roles → run the scenario → debrief with original participants.

- **SRE Education Blueprint** (two axes):
  - X-axis: Abstract (theory) → Applied (hands-on)
  - Y-axis: Time (pre-on-call → post-on-call)
  - Going on-call is the milestone; before it, structured concrete experiences; after it, self-directed.

- **Five practices for aspiring on-callers**:
  1. Learn and use the production environment and its tools
  2. Read postmortems on your service and related services
  3. Shadow the on-call before becoming primary
  4. Reverse-engineer systems from fundamentals and monitoring data
  5. Write and maintain runbooks / on-call documentation

## Key Concepts
- **Trust as the foundation of on-call**: Team members must trust that each on-caller can: understand system behavior, diagnose anomalies, escalate appropriately, and perform under pressure.
- **Learning anti-patterns** (avoid these):
  - "Trial by fire" / deluge with ticket triage
  - Training only with procedures, checklists, and playbooks (no principles)
  - First hands-on experience after already on-call
  - Keeping expertise compartmentalized in senior SREs
- **Concrete sequential learning**: New SREs need structured experiences in order. Jumping to "shadow on-call" without system knowledge is ineffective.
- **Reverse engineering value**: New SREs asking "why does this work this way?" often identify documentation gaps and stale assumptions.
- **Ongoing education for seniors**: Postmortem reading clubs, cross-team exercises, teaching others — all keep senior SREs current as systems evolve.

## Reference Tables
| Recommended Pattern | Anti-pattern |
|---|---|
| Structured sequential learning | Deluging with menial work (trial by fire) |
| Reverse engineering + first principles | Training only via checklists/playbooks |
| Postmortems as learning material | Treating outages as secrets |
| Contained breakage exercises | First fix after already on-call |
| Role-playing (Wheel of Misfortune) | Compartmentalized expert knowledge |
| Shadow on-call with comparison notes | Push to primary on-call prematurely |
| Nontrivial project work for juniors | All projects to senior SREs |

## Key Takeaways
1. Trust must be earned before on-call, not during it. Structured education builds the confidence that enables effective on-call.
2. Wheel of Misfortune is the most high-value training technique — combines postmortem learning with practiced incident management.
3. Shadow the on-call before becoming primary — observe how the current on-caller handles pages, builds pattern recognition.
4. Reverse engineering systems from fundamentals + monitoring data accelerates deep understanding faster than reading documentation.
5. Teaching others is the best way for seniors to stay sharp and identify their own knowledge gaps.

## Connects To
- **Ch 15**: Postmortems — the raw material for Wheel of Misfortune exercises.
- **Ch 11**: Being On-Call — the destination of the education journey.
- **Ch 12**: Effective troubleshooting — the core skill developed through reverse engineering and breakage exercises.
