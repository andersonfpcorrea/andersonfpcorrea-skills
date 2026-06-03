# Chapter 29: Dealing with Interrupts

## Core Idea
Interrupts (pages, tickets, ongoing ops) are the primary source of toil and the enemy of flow state. Managing operational load requires structural choices about who handles what, not individual heroics.

## Frameworks Introduced
- **Three categories of operational load**:
  1. **Pages**: Production alerts; SLO measured in minutes. Managed by primary on-call.
  2. **Tickets**: Customer requests; SLO measured in hours/days. Assigned to on-call, secondary, or dedicated ticket person.
  3. **Ongoing operational activities**: Rollouts, ad hoc requests; no defined SLO but interrupt-capable.

- **Cognitive flow state**: Deep focus that increases productivity, creativity, and mastery. Interrupts destroy it. Designing interrupt handling to protect flow state is an engineering requirement, not a personal preference.

## Key Concepts
- **Single on-call philosophy**: One person receives pages per shift, not the whole team. Prevents bystander effect; focuses interrupt load. Escalation path exists for hard problems.
- **Interrupt SLO metrics**: Response time, backlog size, severity, frequency, coverage (number of people who can handle a given interrupt type). Use these to size the interrupt-handling structure.
- **Bystander effect in on-call**: If everyone sees the page, no one takes ownership. Single primary on-call forces clear ownership.
- **Operational load categories for teams**: Categorize interrupts, measure them, and structure response accordingly. Ticket backlog > N → add dedicated ticket person. Pages per shift > 2 → fix the underlying system.
- **Context-switching cost**: Each interrupt typically costs 20+ minutes of productivity (time to re-enter flow state). Multiple interrupts per hour = near-zero productive engineering time.
- **Interrupts as toil source**: "SREs consistently report interrupts as the #1 source of toil" (from Ch 5). Structural management is the solution, not working faster.
- **"Imperfect machines"**: Humans are not well-suited to interrupt-driven work. Design systems that acknowledge this rather than demanding superhuman focus.

## Mental Models
- Design interrupt handling like you design a queuing system: capacity, SLOs, service rates, overflow handling.
- Protecting flow state is not selfish — interrupted engineers make mistakes, produce lower quality work, and burn out faster.
- Tickets and pages are predictable; ongoing ops are not. Separate handling structures for each.

## Anti-patterns
- **Whole-team pager rotation**: No clear owner = slow response and knowledge fragmentation.
- **No ticket SLOs**: Tickets without SLOs expand to fill all available time.
- **Ignoring bystander effect**: "Someone else will get it" → pages go unanswered.
- **Ops work flowing to anyone free**: Creates unpredictable interrupt load; no one can plan project time.

## Key Takeaways
1. Three categories of operational load: pages (minutes), tickets (hours/days), ongoing ops (variable). Each needs its own management structure.
2. Single primary on-call per shift prevents bystander effect and creates clear ownership.
3. Flow state is real — design interrupt handling to protect it, not just to minimize response time.
4. Measure your interrupt load: frequency, severity, backlog size. Use these metrics to size the response structure.
5. Interrupts are the #1 source of toil. Structural management (rotation assignments, SLOs, dedicated ticket person) reduces them more than individual discipline.

## Connects To
- **Ch 5**: Eliminating toil — interrupts are the primary toil source.
- **Ch 11**: Being On-Call — pages are the most time-critical interrupt category.
- **Ch 30**: Embedding an SRE — when interrupt load overwhelms a team, embedding is the intervention.
