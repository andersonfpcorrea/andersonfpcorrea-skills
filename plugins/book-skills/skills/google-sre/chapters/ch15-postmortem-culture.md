# Chapter 15: Postmortem Culture: Learning from Failure

## Core Idea
A postmortem is not punishment — it is a learning artifact. Blameless postmortems are the cornerstone of an organization that learns from failure rather than hiding it.

## Frameworks Introduced
- **Blameless Postmortem**: A written record of an incident assuming everyone had good intentions and did the right thing with the information they had. Focus: systematic causes, not individual fault.
  - Key principle: "You can't fix people, but you can fix systems and processes."

- **Postmortem Triggers** (write one when any of these occur):
  - User-visible downtime or degradation beyond threshold
  - Data loss of any kind
  - On-call engineer intervention (rollback, rerouting)
  - Resolution time above threshold
  - Monitoring failure (manual incident discovery)

- **Postmortem Review Criteria** (completeness checklist):
  - Was key incident data collected for posterity?
  - Are impact assessments complete?
  - Was root cause analysis sufficiently deep?
  - Is the action plan appropriate and are bugs at correct priority?
  - Did we share with all relevant stakeholders?

- **Postmortem Culture Reinforcement Activities**:
  - Postmortem of the month (newsletter)
  - Postmortem reading clubs (team events)
  - Wheel of Misfortune (new SRE training — see Ch 28)
  - Postmortem + peer bonus visibility at all-hands

## Key Concepts
- **Blameless ≠ consequence-free**: Blameless means focusing on system causes, not indicting individuals. Action items and system fixes absolutely follow.
- **No postmortem left unreviewed**: An unreviewed postmortem might as well not exist. Regular review sessions close out discussions and finalize action items.
- **Transparent sharing**: Share postmortems to the widest audience that benefits from the knowledge. Broad sharing prevents siloed learning.
- **Postmortem as cultural norm**: At Google, "any significant incident is followed by a comprehensive postmortem" — this was achieved through sustained reinforcement, not mandate alone.
- **Cost vs. value tension**: Critics question postmortem ROI. Counter: successful postmortems + rewarded practice + leadership participation makes the value visible.
- **Machine learning on postmortems**: Future work includes predicting weaknesses from postmortem trend analysis — postmortems as training data.

## Mental Models
- Blame locates failure in a person (who you then fire or punish). Blameless analysis locates failure in the system (which you then fix).
- "Removing blame from a postmortem gives people the confidence to escalate issues without fear." Fear of blame → hiding problems → bigger surprises later.
- Finger-pointing example vs. blameless example: "Rewrite the backend!" (frustration) vs. "An action item to rewrite the backend might prevent these pages" (constructive).
- Visibly reward: when founders celebrate good incident handling at all-hands, the signal is clear — postmortems are valued.

## Anti-patterns
- **Postmortem as blame assignment**: Creates fear of escalation, leads to swept-under-rug culture.
- **Stigmatizing frequent postmortem writers**: A team that writes many postmortems is learning; that's good.
- **Unreviewed postmortems**: Incomplete action items never get fixed. Review is not optional.
- **Private postmortems**: Learning is limited to the team. Cross-team pattern recognition becomes impossible.
- **Skipping triggers to save time**: Not writing when you should leads to repeating incidents "ad infinitum."

## Key Takeaways
1. Blameless = focusing on systemic causes, not individual fault. You cannot fix people, but you can fix systems.
2. Pre-define postmortem triggers before an incident — agreement in advance prevents arguments after.
3. Every postmortem must be reviewed; unreviewed = wasted effort.
4. Share broadly — postmortems written for one team and filed away teach only that team.
5. Culture requires continuous reinforcement: reading clubs, public recognition, leadership participation, monthly features.

## Connects To
- **Ch 12**: Troubleshooting — the root-cause analysis technique that feeds postmortems.
- **Ch 13**: Emergency response — every significant emergency produces a postmortem.
- **Ch 14**: Managing incidents — the incident doc is the raw material for the postmortem.
- **Ch 28**: Accelerating SREs — Wheel of Misfortune uses historical postmortems for training.
- **Appendix D**: Example postmortem template.
