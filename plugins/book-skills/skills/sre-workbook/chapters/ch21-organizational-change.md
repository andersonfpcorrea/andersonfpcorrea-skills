# Chapter 21: Organizational Change Management in SRE

## Core Idea
Introducing SRE is organizational change. Change management frameworks (Kotter, ADKAR, Deming, Lewin) apply directly to SRE adoption; understanding which stage your org is in predicts the interventions that will work.

## Frameworks Introduced

### Change Management Models Applied to SRE

| Model | Core insight for SRE adoption |
|-------|-------------------------------|
| **Lewin's 3-Stage**: Unfreeze → Change → Refreeze | SLO adoption fails if you skip Unfreeze; org must accept current state is insufficient before changing. |
| **Kotter's 8 Steps** | Creating urgency (reliability incidents) precedes building the guiding coalition (exec + SRE). Steps can't be skipped. |
| **ADKAR** (Awareness, Desire, Knowledge, Ability, Reinforcement) | SRE adoption fails when one ADKAR element is missing; diagnose which element is the gap. |
| **Deming Cycle** (Plan-Do-Check-Act) | SRE improvement is continuous; SLO reviews are the "Check" phase; error budget policy is the "Act" trigger. |

### ADKAR Diagnosis for SRE Adoption
- **Awareness missing**: people don't know why reliability matters; fix with data (incident cost, customer churn correlation with downtime).
- **Desire missing**: people understand but don't want to change; fix by demonstrating personal benefit (less on-call pain, career growth).
- **Knowledge missing**: people want to change but don't know how; fix with training, worked examples, templates.
- **Ability missing**: people know what to do but can't do it with current tools; fix with tooling investment.
- **Reinforcement missing**: change happens briefly then reverts; fix with process changes (error budget policy, ProdEx reviews).

## Key Concepts
- **Change velocity**: SRE involves continuous change (releases, config changes); ~70% of incidents at Google are caused by binary or config pushes. Change is necessary and dangerous; manage it, don't prevent it.
- **Error budget as change lever**: error budget policy creates a feedback mechanism — the organization slows changes automatically when reliability is at risk. This is organizational change management embedded in the process.

## Waze Case Study: Two Waves of Change
**Wave 1: Messaging queue replacement**
- Replaced a critical system while maintaining reliability.
- Used feature flags + canary to avoid big-bang migration.
- Key lesson: parallel running of old and new systems with gradual traffic shift prevents data loss and rollback disasters.

**Wave 2: Deployment process improvement**
- After messaging queue success, team had credibility to improve the deployment pipeline.
- Changed from manual-approval deploys to automated CI/CD with canary validation.
- Key lesson: build credibility with small wins first; then tackle larger changes.

## Common Tooling Adoption Case Study
- SRE team needed to migrate 50+ teams to a common monitoring platform.
- Used ADKAR: published incident data showing value (Awareness); ran opt-in pilot (Desire); provided migration templates (Knowledge); automated migration tooling (Ability); embedded adoption into team health reviews (Reinforcement).
- Result: 80% adoption in 6 months vs. projected 18 months.

## Anti-patterns
- **Mandate without support**: "everyone will use SLOs by Q4" with no training or tooling.
- **Skipping Kotter's urgency step**: trying to build coalition before leadership sees the problem.
- **One-time training**: without reinforcement mechanisms, behavior reverts.
- **Ignoring emotional models**: change is experienced emotionally; engineering-only communication of why SRE matters fails to create desire.

## Key Takeaways
1. Diagnose your org's ADKAR gap before prescribing SRE interventions.
2. Start with credibility-building quick wins; tackle systemic change after trust is established.
3. Error budget policy is Reinforcement in ADKAR — without it, SLO culture reverts.
4. ~70% of incidents are release-related; change management is reliability management.

## Connects To
- **Ch 20**: SRE team lifecycle — organizational maturity model that this change management enables
- **Ch 2**: Error budget policy (Appendix B) is the organizational change management artifact
