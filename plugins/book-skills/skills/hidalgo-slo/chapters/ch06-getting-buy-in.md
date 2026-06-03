# Chapter 6: Getting Buy-In

## Core Idea
SLOs require broad organizational alignment to be effective. Engineering is more than code — the reliability conversation must include product, operations, QA, legal, and executive leadership, each with their own concerns that SLOs can address.

## Key Stakeholders and Their Concerns

| Stakeholder | What They Care About | SLO Pitch |
|---|---|---|
| Engineering | Stop fire-fighting, reduce alert fatigue, justify reliability work | SLIs replace noisy alerts; error budgets justify reliability sprints |
| Product | Ship features; know when reliability is blocking progress | Error budgets clarify when to ship vs. stabilize |
| Operations | Clear escalation criteria; reduce pager burden | SLO-based alerting reduces false positives |
| QA | Verification that services meet quality targets | SLIs are the same concept as interface tests |
| Legal | Contract compliance, SLA coverage | Internal SLOs protect SLA headroom |
| Executive leadership | Business performance, customer retention | User-centric reliability data maps to revenue and retention |

## Order of Operations for Getting Buy-In

1. Start with engineering leadership — they gatekeep resources and roadmaps.
2. Bring product along early — SLIs are user journeys they already care about.
3. Present to operations — show how SLO alerting reduces on-call burden.
4. Involve QA — frame SLIs as instrumented interface tests.
5. Legal and compliance last — after the data and tooling are in place.
6. Executive sponsor — valuable for unblocking cross-team conflicts.

## Common Objections and Responses

- **"We're too busy to implement SLOs"** → Start with just one SLI. No SLO needed at first; just build the measurement habit.
- **"Our service is too complex to define SLIs"** → Complex services still have user-observable behaviors. Start at the highest level.
- **"99.9% seems arbitrary"** → It is — that's why we derive it from historical data and user research, not from convention.
- **"Error budgets will freeze our releases"** → They give a shared language for discussing trade-offs; they don't mandate freezes.

## The First Error Budget Policy as Cultural Test

The first error budget policy is the moment culture meets implementation. If the team wrote the policy but ignores it when the budget is exhausted, the SLO program is decorative. The policy must have organizational backing — which is why buy-in must precede implementation.

## Lessons Learned

- Buy-in from multiple stakeholders simultaneously is harder but stickier than sequential conversion.
- Pilot with a visible service where success can be demonstrated, not an obscure microservice.
- Don't wait for perfect tooling to get started — spreadsheet-level error budget tracking is sufficient to demonstrate the concept.

## Anti-patterns

- **Going straight to engineering without product alignment**: Product will override engineering priority decisions if they haven't bought in.
- **Implementing SLOs for a service nobody cares about**: Pilots should use visible, impactful services.
- **Skipping the error budget policy**: SLO data without an agreed response plan is unused data.

## Key Takeaways

1. Different stakeholders need different pitches — tailor the message to their concerns.
2. Start with engineering and product; get executive sponsorship for cross-team conflicts.
3. The first error budget policy is the cultural acid test of the program.
4. Common objections have standard responses — prepare them in advance.
5. Pilot with a small, visible service and document the results as a case study.

## Connects To

- **Ch 5**: Error budget policy design
- **Ch 13**: Building SLO culture organization-wide
- **Ch 16**: SLO advocacy at scale (the "Crawl, Walk, Run" model)
