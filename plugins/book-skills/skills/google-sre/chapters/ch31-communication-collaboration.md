# Chapter 31: Communication and Collaboration in SRE

## Core Idea
SRE teams serve two masters (product teams + SRE org), communicate via production meetings as a core ritual, and collaborate by bringing production wisdom to bear on product design decisions.

## Frameworks Introduced
- **Production Meeting**: Weekly 30–60 minute service-oriented meeting (not status updates for individuals) that:
  - Articulates the current state of services to all stakeholders
  - Connects operational performance to design/configuration/implementation decisions
  - Creates a shared understanding of production health
  - Rotating chair = shared ownership + incident coordination skill development

- **Viceroy case study**: SRE built a unified dashboard (Viceroy) that visualized production state across all Google services, enabling cross-team observability. Demonstrates SRE-driven collaborative tooling.

- **DFP to F1 migration**: SRE facilitated a major data platform migration (from DFP to F1 database) by coordinating across multiple engineering teams, managing the reliability risk during transition. Demonstrates SRE as technical neutral party in cross-team collaboration.

## Key Concepts
- **"Two masters" organizational model**: SRE reports to SRE org hierarchy but is accountable to product development team for service reliability. This creates strong shared culture across SRE.
- **API-as-contract metaphor for communication**: "If the API is wrong, it can be painful to correct later on." Communication contracts between SRE and product teams must be designed carefully.
- **Production meeting structure**: Chair (rotate), service health review, connection between ops data and design decisions, action items with owners.
- **Production meetings ≠ status meetings**: Focus on the service, not individuals. Goal: everyone leaves with the same understanding of what's happening.
- **Cross-SRE collaboration**: Shared values and SRE-wide frameworks (SLO, error budget, postmortem culture) enable SREs from different teams to collaborate without bureaucratic overhead.
- **External communication (user-facing)**: SRE participates in defining external SLAs, communicating maintenance windows, and managing user-facing incident communications.

## Mental Models
- Production meetings are a "feedback loop between service design and operational reality." Design decisions that create operational pain must be visible to both dev and SRE.
- "Culture beats strategy every time" (Attributed to Peter Drucker) — shared SRE culture produces consistent behavior more reliably than any top-down directive.
- SRE as neutral party in cross-team technical disputes: production data provides objective ground truth.

## Anti-patterns
- **Production meeting as status report**: "What did everyone work on?" ≠ "What is the state of our service?" The first is about people; the second is about the system.
- **No rotating chair**: Concentrates institutional knowledge and incident coordination skill in one person.
- **SRE isolated from dev**: Without regular joint production meetings, design decisions that generate toil go unchallenged.
- **Too-frequent or too-long production meetings**: Weekly 30–60 min is the sweet spot; more frequent = insufficient signal accumulation; longer = detail-mired.

## Key Takeaways
1. Weekly production meetings (30–60 min) are the primary mechanism for connecting operational performance to design decisions.
2. Rotate the chair — builds shared ownership and incident coordination skill across the team.
3. SRE's two masters (product team + SRE org) are by design — the tension creates better outcomes than pure product alignment.
4. Shared SRE culture (SLO, error budget, postmortem) enables cross-team collaboration without bureaucratic overhead.
5. Production data is the objective ground truth in design disputes — SRE brings production wisdom to dev conversations.

## Connects To
- **Ch 32**: SRE engagement model — production meetings are how the ongoing engagement operates.
- **Ch 4**: SLOs — the framework that structures production meeting agenda items.
- **Ch 15**: Postmortems — reviewed and discussed in production meetings.
