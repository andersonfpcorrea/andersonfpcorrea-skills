# Chapter 14: Managing Incidents

## Core Idea
Unmanaged incidents spiral because everyone acts heroically and independently. The Incident Command System provides recursive role separation that enables calm, coordinated response at any scale.

## Frameworks Introduced
- **Incident Command System (ICS)** — Google's adaptation:
  - **Incident Commander (IC)**: Holds high-level state; assigns all roles; only person authorized to delegate. Default holder of all undelegated positions.
  - **Ops Lead**: Only group modifying the system during the incident. Reports to IC.
  - **Communications Lead**: Public face; periodic updates to stakeholders and team; maintains incident document.
  - **Planning Lead**: Longer-term issues — filing bugs, ordering dinner, tracking system divergence from norm, arranging handoffs.

- **Clear Handoff Protocol**: "You're now the incident commander, okay?" — explicit verbal acknowledgment required. Don't leave the call until firm acknowledgment received. Communicate handoff to all working on the incident.

- **Anatomy of an Unmanaged Incident** (three failure patterns):
  1. **Sharp technical focus**: IC too busy fixing to manage; big picture lost.
  2. **Poor communication**: No one knows what others are doing; VPs frustrated; engineers duplicating work.
  3. **Freelancing**: Well-intentioned engineers making unauthorized changes, making things worse.

## Key Concepts
- **Incident declaration threshold**: When should you declare an incident? Any time the situation requires structure to manage well. Better to declare early and stand down than to scramble without structure.
- **Live incident document**: Always-updated, multiply-editable (Google Docs). Must be functional even if messy. Template at front; most important info at top. Retained for postmortem and meta-analysis.
- **Central command post**: IRC, video call, or war room — all participants must know where to find the IC. IRC provides a log of all incident communications.
- **Separate Operations from everything else**: ONLY the Ops team touches the system during an incident. Everyone else observes, communicates, or plans — but does NOT modify production.

## Mental Models
- "Recursive separation of responsibilities allows individuals more autonomy, since they need not second-guess their colleagues."
- Without structure: many smart people, each with incomplete information, making contradictory changes simultaneously = faster failure.
- With structure: one person per role, each with clear scope, each reporting up the chain = coordinated response at scale.
- "Keeping the most important information at the top makes it more usable." — incident doc design principle.

## Anti-patterns
- **"I'll just make this one change to help"**: Freelancing that isn't coordinated with Ops causes incidents within incidents.
- **VPs driving technical decisions**: Management escalation is handled by the Communications lead; it must not interrupt the IC or Ops team.
- **No designated commander**: "everyone is responsible" = no one is responsible; decisions can't be made cleanly.
- **Implicit handoff**: "I think you have it from here" ≠ a safe handoff. Explicit verbal acknowledgment is required.

## Reference Tables
| Role | Primary Responsibility | May Modify Production? |
|---|---|---|
| Incident Commander | High-level state, role assignment | No |
| Ops Lead | Technical remediation | Yes (only role) |
| Communications | Updates, docs, stakeholder comms | No |
| Planning | Long-term tracking, logistics | No |

## Key Takeaways
1. Separate Incident Commander (who manages) from Ops Lead (who acts) — the IC never touches production.
2. Only Ops touches the system — freelancing by well-meaning engineers is a major source of incident escalation.
3. Live incident document is the ground truth — everyone reads it before acting, everyone writes to it after acting.
4. Handoffs require explicit verbal acknowledgment, "You're now the IC, okay?" and confirmation before the current IC leaves.
5. Declare incidents early — the structure helps even before the full scope is understood.

## Connects To
- **Ch 12**: Effective troubleshooting — Ops Lead applies the troubleshooting process under IC coordination.
- **Ch 15**: Postmortems — every significant incident produces one.
- **Appendix C**: Sample incident state document template.
