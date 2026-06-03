# Chapter 17: Identifying and Recovering from Overload

## Core Idea
SRE teams experience two kinds of overload: technical (services under too much traffic) and organizational (teams under too much work). Both require early recognition and deliberate mitigation strategies.

## Frameworks Introduced
- **Overload recognition checklist**:
  - Are engineers regularly working outside their working hours?
  - Are on-call engineers unable to complete their ticket queue?
  - Is the postmortem backlog growing?
  - Are toil-reduction projects being deprioritized repeatedly?
  - Is morale declining? Are engineers discussing leaving?

  Two or more "yes" answers = overload signal requiring management action.

- **Overload recovery strategies** (ordered by disruption level):
  1. Reduce incoming work: negotiate scope with stakeholders; say no to new projects.
  2. Improve tooling to handle existing work faster.
  3. Temporarily increase headcount (contractors, rotations from other teams).
  4. Escalate SLOs or error budget policy to force reliability investment.
  5. Hand back services to development teams (reducing SRE surface area).

## Key Concepts
- **Load vs. overload**: load is manageable; overload is unsustainable. The transition is gradual and often invisible until it causes attrition.
- **Perceived overload**: sometimes a team is within capacity but perceives overload due to poor work organization, unclear priorities, or unacknowledged toil. Diagnose before assuming headcount fixes the problem.
- **Org change as trigger**: team merges, re-orgs, product ownership transfers — these are common overload triggers that require proactive load assessment.

## Case Study 1: Half Team Leaves
- Team lost half its members; remaining engineers immediately overloaded.
- Resolution: temporarily reduced service scope; hired contract SREs; negotiated feature freeze on highest-toil services.
- Key lesson: have a documented "minimum viable SRE" plan per service before attrition happens.

## Case Study 2: Perceived Overload After Re-org
- New management structure added process overhead without adding technical work.
- Team reported overload; audit revealed actual technical load was unchanged.
- Resolution: streamlined approval processes; moved low-value meetings to async.
- Key lesson: organizational friction masquerades as technical overload; audit both.

## Capacity Planning for SRE Teams
- Track operational load (pages, tickets, postmortems) as a time series; set an alert when 4-week moving average exceeds 50% of team capacity.
- Include overhead (meetings, reviews, training) in capacity calculations — it's real work.
- Reserve 20% of team capacity as buffer; teams running at 100% have no resilience to spikes.

## Anti-patterns
- **Hiring as the only solution**: adding engineers to an overloaded team increases coordination overhead before it reduces load.
- **Heroics as the new normal**: if overload is consistently solved by individuals working extra hours, it will recur and worsen attrition.
- **No minimum-viable-SRE documentation**: when a team is overloaded, the first thing to cut is unclear without this.

## Key Takeaways
1. Recognize overload early via measurable signals (on-call queue growth, postmortem backlog, attrition signals).
2. Reducing scope is faster than adding headcount; both have a role.
3. Audit whether overload is technical or organizational before prescribing solutions.
4. Reserve team capacity buffer; 100% utilization is fragile.

## Connects To
- **Ch 6**: Toil elimination is the first-line tool for reducing operational load
- **Ch 20**: SRE team lifecycles — building sustainable team structures from the start
