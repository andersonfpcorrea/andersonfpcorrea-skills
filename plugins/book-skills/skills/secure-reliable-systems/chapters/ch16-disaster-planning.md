# Chapter 16: Disaster Planning

## Core Idea
Disaster response capability must be designed, practiced, and regularly tested before disasters occur — because well-practiced incident management skills are the only reliable response to emergencies, and untested procedures fail when needed.

## Frameworks Introduced
- **Disaster Risk Analysis**: Systematically rank potential disasters by probability × impact to identify the most critical operational dependencies and focus preparedness investment. The risk matrix (Appendix A) provides a structured format: classify systems as mission-essential, mission-important, or nonessential; rank scenarios by likelihood × impact.
- **Dynamic Disaster Response Phases**: Immediate response → Short-term recovery (with exit criteria) → Long-term recovery → Resumption of operations. Plan for each phase separately; define exit criteria for the short-term phase before the incident.
- **IR Team Structure**: Incident Commander (leads response) + functional roles (SREs, PR, legal, customer support, forensics, executive communications). Staff model: dedicated full-time vs. dual-hat vs. outsourced.
- **Prestaging**: Configure systems, train responders, and establish procedures *before* an incident — not during. Prestaging reduces response time and error rate under pressure.

## Key Concepts
- **DiRT (Disaster Recovery Testing)**: Google's program that regularly simulates internal system failures and forces teams to respond. Exercises both the infrastructure (does the failover work?) and the humans (do responders know what to do?).
- **Tabletop exercises**: Low-risk, nonintrusive simulations where teams walk through an incident scenario without touching production. Builds familiarity with procedures; surfaces gaps in playbooks.
- **Red Team testing**: Offensive security exercises that simulate real adversaries attacking the system; tests detection and response capabilities, not just prevention.
- **Playbooks**: Pre-written step-by-step response procedures for anticipated incident types. Should be specific enough to execute under stress but flexible enough to handle variations.
- **Incident severity and priority models**: Formalize what constitutes a P1 vs. P2 incident; define escalation thresholds. Teams need consistent shared language to avoid ambiguity during crises.
- **Exit criteria for incidents**: Define before the incident what "resolved" means — what state must the system be in to declare the incident closed? Without exit criteria, incidents drag on indefinitely.
- **"Incident response isn't like riding a bicycle"**: Without regular practice, responders lose muscle memory. Practice is not optional.

## Reference Table: Disaster Response Phases
| Phase | Focus | Key Activity |
|---|---|---|
| Immediate response | Contain damage | Execute preplanned playbooks; establish IC |
| Short-term recovery | Restore operations | Execute recovery checklist; track exit criteria |
| Long-term recovery | Eliminate root cause | Redesign/patch affected systems; postmortem |
| Resumption of operations | Return to normal | Validate restored state; update procedures |

## Anti-patterns
- **Disaster plan as a document no one has read**: Plans that exist only on paper provide no value. Teams must practice until procedures are second nature.
- **Only planning for likely disasters**: Improbable but high-impact events (the tail risks) may be the most important to prepare for.
- **No exit criteria for incidents**: Without a definition of "resolved," incidents stay open indefinitely and drain responder energy.

## Key Takeaways
1. Disaster risk analysis → prioritize preparedness investment by probability × impact; classify systems as mission-essential vs. others.
2. Practice is the only way to maintain incident response capability — DiRT, tabletops, and red team exercises must be scheduled and repeated.
3. Prestage everything: access credentials, communication channels, playbooks, and designated responders must be ready before an incident.
4. Define exit criteria for incidents before they start — what state must the system reach for the incident to be declared closed?
5. A dedicated IC role separates management/coordination from technical execution — responders execute; the IC maintains the big picture.

## Connects To
- **Ch 17**: Crisis management — IMAG framework in action
- **Ch 18**: Recovery — executing the recovery after an incident
- **Ch 9**: Recovery design — building recoverable systems
- **Ch 15**: Investigation — forensics and logging during disaster response
