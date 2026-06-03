# Chapter 11: Being On-Call

## Core Idea
On-call is a cognitive and human challenge, not just a technical one. Sustainable on-call requires structural constraints on both quantity (time spent) and quality (incidents per shift).

## Frameworks Introduced
- **25% On-Call Rule**: Of the 50% non-engineering time, on-call should consume ≤ 25% of each SRE's total time. The remaining 25% is other non-project ops work.
  - Minimum rotation: 8 engineers (single-site, primary only); 6 per site (multi-site).
  - Formula: 2 on-call weeks / N-person rotation × 100% ≤ 25%.
- **Maximum 2 incidents per 12-hour shift**: Each incident averages 6 hours of total work (root-cause, remediation, postmortem). More than 2 prevents thorough investigation and learning.
- **Multi-site "follow-the-sun" rotation**: Avoids night shifts (proven health damage); minimum 6 SREs per site.

## Key Concepts
- **Response time targets**: 5 minutes for user-facing/revenue-critical systems; 30 minutes for less time-sensitive. Tied to SLO: 99.99% availability = 13 min/quarter downtime = SREs must respond in minutes.
- **Cognitive mode under stress**: Stress hormones (cortisol, CRH) degrade deliberate rational thinking and push toward automatic/intuitive responses. Intuitive responses are often wrong for novel failures.
- **Confirmation bias risk**: On the 4th occurrence of an alert that was previously caused by X, SREs automatically assume X again — often wrong, always untested.
- **Three on-call resources that reduce stress**: (1) Clear escalation paths, (2) Well-defined incident-management procedures, (3) Blameless postmortem culture.
- **Operational overload signals**: Median incidents/day > 1 → team is overwhelmed → something needs to change (not just "work harder").
- **Operational underload danger**: SREs who never interact with production lose familiarity with systems; multi-site teams must stay large enough that individuals stay engaged.
- **Compensation model**: Time-off-in-lieu or capped cash compensation. Cap prevents excessive on-call load accumulation.

## Mental Models
- "Deliberate cognitive function beats intuitive action in complex system outages." Use checklists and playbooks to force rational mode.
- Pager load review with management every quarter — on-call health is a management concern, not just individual resilience.
- A team that consistently exceeds 2 incidents/shift must fix the underlying systems, not hire more SREs.
- "A multi-site team is advantageous for two reasons: night shifts damage health, and size limits prevent underload."

## Anti-patterns
- **Hero culture**: Relying on individual SRE heroism creates burnout, single points of failure in the team, and poor institutional learning.
- **Intuitive responses without playbooks**: Confirmation bias causes false root-cause attribution; habits cause disastrous actions.
- **Oversized single-site rotation**: "Follow the sun" is preferable for any team large enough to justify a second site.
- **Ignoring pager fatigue signals**: If shift load regularly exceeds 2 incidents, the problem is the system — not the SRE.

## Key Takeaways
1. On-call ≤ 25% of total SRE time; more than 2 incidents per 12-hour shift is a structural problem requiring fixes, not more on-call.
2. Stress hormones degrade decision quality. Blameless culture, clear escalation paths, and playbooks reduce stress and improve outcomes.
3. Multi-site "follow the sun" eliminates night shifts — which are documented health hazards.
4. Both operational overload (>2 incidents/shift) and operational underload (never touching prod) are dangerous.
5. Minimum rotation size: 8 engineers (single-site), 6 per site (multi-site) to honor the 25% rule.

## Connects To
- **Ch 1**: The 50% cap — on-call is the main driver of ops time.
- **Ch 5**: Toil elimination — on-call is a toil floor that automation reduces.
- **Ch 12**: Effective troubleshooting — what to do when the page fires.
- **Ch 14**: Managing incidents — the structured response process.
- **Ch 29**: Dealing with interrupts — the other part of the operational load.
