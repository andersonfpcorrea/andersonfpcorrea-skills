# Chapter 6: Eliminating Toil

## Core Idea
Toil is the repetitive, automatable operational work that scales with service size. Google caps SRE toil at 50% of work time. Measuring toil objectively is the prerequisite to eliminating it.

## Frameworks Introduced
- **Toil characteristics** (a task is toil if it has these properties):
  1. Manual — requires human action, not just human judgment
  2. Repetitive — occurs more than once
  3. Automatable — could be done by a machine; runbooks are pseudocode
  4. Nontactical/reactive — interrupts higher-value work; masks other alerts
  5. Lacks enduring value — resolving today doesn't prevent recurrence
  6. Grows at least as fast as its source — scales with infrastructure size

- **Toil taxonomy** (six categories):
  1. Business processes — approval workflows, provisioning forms
  2. Production interrupts — alerts and tickets requiring human response
  3. Release shepherding — manual steps in the release pipeline
  4. Migrations — moving data, services, or users between systems
  5. Cost engineering and capacity planning — manual sizing and forecasting
  6. Troubleshooting for opaque architectures — debugging undocumented systems

- **Toil measurement process**:
  1. Identify the toil (use Ch 5 of first SRE book as guide)
  2. Choose an objective unit of measure (hours, tickets, patches applied)
  3. Track continuously before, during, and after elimination efforts
  4. Calculate ROI: time saved ÷ time invested (but account for indirect benefits)

## Key Concepts
- **50% toil cap**: SRE teams at Google spend ≤50% on operational work (including toil and non-toil ops). This cap enforces that engineering remains in the job description.
- **ROI of automation**: Direct time savings may look slim; indirect benefits (morale, fewer outages, career growth, security) often dwarf direct savings.
- **Root cause vs. symptom**: Writing a script to mask a symptom defers the root-cause fix and removes the incentive for the team that owns the problem to fix it properly.

## Toil Management Strategies (Ordered by Impact)
1. Identify and measure toil — you can't reduce what you don't track
2. Engineer toil out of the system — fix root causes, not symptoms
3. Reject the toil — say no to work that should belong to another team
4. Use SLOs to reduce toil — if a service within SLO, deprioritize its toil work
5. Start with human-backed interfaces, then automate them
6. Provide self-service methods — let service owners do their own routine work
7. Get management support — toil reduction requires allocated time
8. Start small and improve — don't wait for a perfect automation system
9. Increase uniformity — standardized systems generate less unique toil
10. Automate toil response — use runbooks as automation pseudocode

## Case Study Pattern (from Chapter 6)
Both case studies (datacenter automation, filer home directory decommission) share this structure:
1. Identify the toil and its cost
2. Design a small first automation
3. Deploy, measure, iterate
4. Extract reusable components

Key lesson: the second attempt (Jupiter line-card repair) was simpler because the first attempt (Saturn) generated reusable abstractions.

## Anti-patterns
- **Workaround as solution**: masking a problem removes the root-cause fix incentive.
- **Ignoring indirect benefits**: ROI calculations that only count time saved miss most of the value.
- **Toil creep**: without the 50% cap (or equivalent), toil expands to fill available time.

## Key Takeaways
1. A task is toil when it is manual, repetitive, automatable, reactive, valueless, and scales with the system.
2. Measure toil in objective units before, during, and after elimination.
3. Fix root causes; masking symptoms creates organizational incentive problems.
4. Operational work ≤50% is not a goal — it's a constraint on team sustainability.

## Connects To
- **Ch 8**: On-call pager load is the most visible toil source
- **Ch 15**: Configuration specifics and configuration-induced toil
- **Ch 18**: SRE engagement model determines which services get toil-reduction investment
