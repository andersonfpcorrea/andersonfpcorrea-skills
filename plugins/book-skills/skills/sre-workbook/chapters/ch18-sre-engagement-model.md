# Chapter 18: SRE Engagement Model

## Core Idea
SRE engagement follows the service lifecycle; early engagement (design and pre-launch) yields far more reliability value than post-GA firefighting. The relationship must be structured, with explicit ground rules and regular review.

## Frameworks Introduced

### Service Lifecycle Phases (7 stages)
1. **Architecture and Design**: SRE reviews design docs; identifies reliability risks; recommends structural changes.
2. **Active Development**: SRE consults on instrumentation, SLOs, capacity planning.
3. **Limited Availability (LA)**: SRE participates in readiness review; first SLOs established.
4. **General Availability (GA)**: SRE takes on-call; SLOs enforced; error budget policy active.
5. **Deprecation**: SRE helps plan migration; ensures reliability through deprecation window.
6. **Abandoned**: SRE disengages; service handed back or discontinued.
7. **Unsupported**: Service runs without SRE; team responsible for its own on-call.

### Production Readiness Review (PRR) Checklist
Before SRE takes on-call:
- [ ] SLOs defined and measured
- [ ] Monitoring and alerting configured (Ch 4 and Ch 5 standards)
- [ ] Runbooks exist for all alerts
- [ ] On-call handoff process documented
- [ ] Incident response process established
- [ ] Error budget policy agreed and signed
- [ ] Capacity plan reviewed
- [ ] Dependencies identified and failure modes documented

### Engagement Ground Rules
Set explicitly before engagement begins:
- Who owns what? (SRE vs. product team boundary)
- What decisions require SRE sign-off? (releases, load test approvals)
- What is the on-call transfer process?
- How are SLO changes negotiated?
- What is the escalation path when SRE disagrees with a product decision?

## Scaling SRE to Multiple Services
- **One SRE team, multiple services**: use tiered engagement (full on-call for tier 1; consulting only for tier 2; self-serve for tier 3).
- **Multiple SRE teams**: shared tooling platform reduces toil per team; "SRE Exchange" cross-team rotation builds institutional knowledge.
- **Geographic distribution**: requires explicit handoff protocol and co-owned runbooks; do not rely on timezone overlap for incident response.

## Ending the Engagement
- Explicit criteria for disengagement: service is within SLO consistently; on-call burden is low; development team is capable of self-managing.
- Gradual handoff: reduce SRE on-call share over a quarter; don't hard-cut.
- Maintain consulting relationship even post-disengagement.

## New York Times Case Study
- NYT engaged SRE during a major infrastructure migration (on-premise → GCP).
- SRE involvement during design phase prevented multiple architectural decisions that would have required expensive rework post-launch.
- Key lesson: SRE value is highest when engaged before architecture is locked in.

## Anti-patterns
- **SRE engagement without SLOs**: no measurement means no accountability; engagement becomes glorified ops support.
- **Post-GA-only engagement**: SRE arrives after architecture is decided; options are limited to operational patches.
- **No explicit disengagement criteria**: services stay in SRE portfolio indefinitely; team becomes overloaded.

## Key Takeaways
1. Engage SRE at design phase (Phase 1-2); the reliability dividend is far higher.
2. Always have a signed error budget policy before GA.
3. Tiered engagement scales SRE to cover more services with the same headcount.
4. Define explicit disengagement criteria at engagement start — not as an afterthought.

## Connects To
- **Ch 2**: SLO/error budget setup is the prerequisite for all SRE engagement phases
- **Ch 17**: Overload happens when engagement model is not maintained (too many services, no disengagement)
- **Ch 20**: SRE team lifecycle — how team structure evolves with engagement model
