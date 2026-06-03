# Chapter 10: Postmortem Culture: Learning from Failure

## Core Idea
A blameless postmortem culture is a prerequisite for reliable systems. Good postmortems are specific, complete, actioned, and shared; bad postmortems blame individuals, omit details, and create vague action items.

## Frameworks Introduced

### What Makes a Good Postmortem
- **Blameless**: attribute cause to systems, processes, and circumstances — not individuals. Blame inhibits honesty and kills learning.
- **Complete**: full timeline, impact quantified, root cause explained, mitigations documented in recovery efforts.
- **Actioned**: all action items are specific, assigned (with tracking bugs), prioritized differently, and include preventative fixes (not just mitigations).
- **Shared**: published widely so the whole org can learn. Posting to an internal mailing list is not enough; actively route postmortems to related teams.

### Action Item Quality Test
Every AI must answer:
1. **Who** owns it? (named person, not team)
2. **What** specifically must be done? (no "make better", no "improve")
3. **What priority**? (P0/P1/P2 differentiated, not all P2)
4. **What tracking bug**? (required; prevents AIs from being forgotten)
5. **What type**? (prevent / mitigate / detect — include at least one preventative)

### Organizational Incentives for Postmortem Culture
- **Model blameless behavior**: leadership must visibly practice it, especially under pressure
- **Reward postmortem outcomes**: celebrate teams whose action items prevent recurrence; track how many incidents are repeat failures
- **Share openly**: a postmortem shared only within the immediate team is half as valuable
- **Respond to culture failures**: when blame appears in a postmortem, address it immediately and publicly

## The Good vs. Bad Postmortem Comparison

| Dimension | Bad postmortem (anti-pattern) | Good postmortem |
|-----------|-------------------------------|-----------------|
| Executive summary | Names individual as cause | Describes systemic cause |
| Root cause | Shallow — "ignored automation setup" | Deep — API treated empty list as no-filter; non-idempotent automation |
| Recovery efforts | Empty | Full timeline of mitigations attempted |
| Action items | All P2, vague ("make better"), no tracking bugs | Mixed priorities, specific, all have tracking bugs |
| Sharing | Small distribution | Widely shared |
| Language | "careless ignorance", "ridiculous" | Neutral, factual |

## Postmortem Template (minimum sections)
```
Status: [Draft / In Review / Final]
Owner: [single person]
Date: [incident date] Published: [date]

Executive Summary
Impact [quantified: duration, % affected, revenue if known]
Timeline [annotated: detection, escalations, mitigations, resolution]
Root Causes [specific: what failed, why, what conditions required]
Trigger [what initiated the failure]
Resolution [what was done to resolve]
Action Items
  | Action | Type (prevent/mitigate/detect) | Priority | Owner | Bug |
Lessons Learned
  What went well / What went poorly / Where we got lucky
```

## Postmortem Tooling
- Dedicated postmortem tool (Google Docs, Confluence, or specialized tool) with required fields enforces completeness.
- Action-item tracking integrated with bug tracker prevents AIs from being forgotten.
- Searchable archive enables "did we have a similar failure before?" lookup.

## Anti-patterns
- **"Train humans not to make mistakes"** as an AI: human behavior change is the least reliable fix.
- **Publishing too late**: postmortem value decays rapidly; publish draft within 48h.
- **Blame in summary**: identifying a person as root cause removes systemic learning.
- **All action items the same priority**: signals no real prioritization happened.

## Key Takeaways
1. Blame is incompatible with learning; blameless culture is the prerequisite, not a nice-to-have.
2. Action items without owners, tracking bugs, and specific success criteria are not action items.
3. Preventative AIs are more valuable than mitigative ones — plan for a future where humans are as error-prone as they are today.
4. A postmortem shared only with the immediate team has half the impact of a widely distributed one.

## Connects To
- **Ch 9**: Incident response feeds into postmortem; the working doc becomes the timeline
- **Appendix C**: Postmortem analysis data (what fraction of incidents are caused by releases vs. other sources)
