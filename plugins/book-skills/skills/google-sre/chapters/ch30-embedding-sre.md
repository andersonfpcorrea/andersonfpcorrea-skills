# Chapter 30: Embedding an SRE to Recover from Operational Overload

## Core Idea
When a team slides into ops mode, embedding a single SRE consultant (not a rescuer) for a structured 3-phase engagement can break the cycle and restore the engineering/ops balance.

## Frameworks Introduced
- **Three-Phase Embedded SRE**:
  1. **Phase 1: Learn the Service and Get Context** — shadow on-call, understand what generates tickets, identify "kindling" (problems-in-waiting).
  2. **Phase 2: Sharing Context** — present findings to the team; show root-cause analysis of ticket sources; identify automation opportunities; communicate using data.
  3. **Phase 3: Driving Change** — prioritize changes; implement improvements; leave the team with skills and processes, not just fixes.

- **Ops Mode vs. Nonlinear Scaling**: Ops mode = team headcount scales linearly with service complexity. SRE = team scales sublinearly by eliminating the O(n) work through automation and good design.

## Key Concepts
- **Embed one SRE, not two**: Two can cause defensive reactions from the team. One SRE is a consultant; two feels like an audit.
- **"Kindling" identification**: Problems waiting to happen — knowledge gaps, unofficial critical services, "wait for the next big thing" dependencies, common alerts that are never diagnosed.
- **Ticket volume vs. service size**: More tickets should NOT require more SREs. If it does, the service is in ops mode.
- **Fresh perspective value**: Teams can't see their own habits clearly; an embedded SRE sees patterns the team normalizes.
- **Service without SLI/SLO but receiving complaints**: Strong signal of ops mode — no agreement on what "good" means, so all issues become emergencies.
- **"Add more servers" capacity planning**: A sure sign of ops mode — capacity scaling by muscle, not by design.
- **Change driving approach**: After presenting findings, work with the team on a prioritized fix list. Implement the first few improvements alongside the team to build skills, not just fix things.

## Mental Models
- "More tickets should not require more SREs" — this is the fundamental diagnostic.
- The embedded SRE's goal is to change team habits, not to empty the ticket queue.
- Identify stress (current pain) and kindling (future pain) separately — both require attention.
- Teaching the team to fish > fishing for the team. Phase 3 builds capability.

## Anti-patterns
- **Embedding to empty the queue**: Provides temporary relief but doesn't change the underlying habits.
- **Two or more embedded SREs**: Often causes defensive reactions; one is a consultant, two is an intervention.
- **Ignoring the dev/SRE relationship**: Ops mode often reflects unclear boundaries between what dev owns and what SRE owns.
- **Starting with solutions before understanding**: Phase 1 (learn first) must precede Phase 3 (change).

## Key Takeaways
1. Ops mode symptom: ticket volume grows with service size, requiring proportional headcount. This is fixable.
2. Embed one SRE (not two) for consulting, not ticket-draining.
3. Three phases: learn context → share findings with data → drive systemic change.
4. "Kindling" — problems waiting to happen — is as important to identify as current problems.
5. The goal is transferring skills and changing habits, not just fixing the immediate queue.

## Connects To
- **Ch 5**: Eliminating toil — ops mode is the pathological case where toil > 50%.
- **Ch 29**: Dealing with interrupts — the interrupt load that signals ops mode.
- **Ch 32**: SRE engagement model — embedding is one engagement model in the broader framework.
