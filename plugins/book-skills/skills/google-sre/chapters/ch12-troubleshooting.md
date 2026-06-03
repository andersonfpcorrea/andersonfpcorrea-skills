# Chapter 12: Effective Troubleshooting

## Core Idea
Troubleshooting is a learnable skill based on the hypothetico-deductive method: form hypotheses, test against observations, narrow to root cause. Triage (stopping the bleeding) always precedes root-cause analysis.

## Frameworks Introduced
- **Troubleshooting Process** (iterative loop):
  1. Receive problem report → understand expected vs. actual behavior
  2. Triage → assess severity, make system work as well as possible *now*
  3. Examine → gather telemetry, form hypotheses about cause
  4. Diagnose → test hypotheses (compare observations or change system)
  5. Test/Treat → apply fix, verify effect
  6. Root-cause → write postmortem, prevent recurrence

- **"Fly the airplane first"**: In a major outage, stabilize the system before attempting root-cause analysis. Divert traffic, drop load, disable subsystems — stop the bleeding first.

- **Negative results are magic**: Ruling out a hypothesis is as valuable as confirming one. Publish negative results ("it's NOT a database issue") so others don't re-investigate the same dead end.

## Key Concepts
- **Two failure modes of troubleshooting**:
  1. Generic process skill (applicable to any system) — learnable from this chapter
  2. System knowledge (how this specific system works) — comes from study and experience
- **Problem report quality**: Must include expected behavior, actual behavior, and reproduction steps. File every issue as a bug, even if reported via chat — creates searchable history.
- **Common pitfalls**:
  - Looking at irrelevant symptoms (wild goose chases)
  - Misunderstanding how to change the system to test hypotheses
  - Latching onto past failure modes ("it was the database before")
  - Hunting spurious correlations (correlation ≠ causation)
- **"Think horses, not zebras"**: When you hear hoofbeats, consider common causes first. Not all failures are equally probable.
- **Occam's Razor caveat**: Multiple common problems explain symptoms better than one exotic problem — Hickam's dictum.
- **Making troubleshooting easier** (systemic improvements): Build systems with clean separation of concerns; make each component easily observable; design components to have minimal external dependencies; instrument everything.

## Mental Models
- "Your first response in a major outage may be to start troubleshooting — ignore that instinct!" Stabilize first.
- Treat the system like a pilot treats the airplane — fly first, diagnose second.
- Each hypothesis test should have a clear predicted outcome; if you can't predict the outcome, you're not really testing a hypothesis.
- "Ways in which things go right are special cases of the ways in which things go wrong." (John Allspaw) — Study failure modes.

## Anti-patterns
- **Root-causing before stabilizing**: While you're investigating, users are still down. Mitigate first.
- **Reporting to individuals vs. bugs**: Concentrates load on known experts; creates no searchable record.
- **Confirmation bias**: Assuming the current failure matches a previous one without verifying.
- **Correlation as causation**: Two things happened at the same time; therefore one caused the other. Test the causal model.

## Key Takeaways
1. Triage (stabilize) before diagnosis — stop the bleeding before finding the wound.
2. Hypothetico-deductive method: form a hypothesis, predict its observable implications, test against data.
3. Negative results are as valuable as positive ones — share them to prevent redundant investigation.
4. Common failures first (horses before zebras); verify rather than assume.
5. File every problem as a bug, even verbal reports — creates the historical record needed for pattern recognition.

## Connects To
- **Ch 6**: Monitoring — telemetry and logs are the raw material for step 3 (Examine).
- **Ch 13**: Emergency response — case studies of real incidents following this process.
- **Ch 14**: Managing incidents — the organizational wrapper around troubleshooting.
- **Ch 15**: Postmortems — the output of step 6 (root-cause).
