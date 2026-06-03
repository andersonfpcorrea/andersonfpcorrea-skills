# Chapter 1: Introduction

## Core Idea
SRE resolves the structural conflict between dev and ops by making reliability a shared engineering problem with an objective metric — the error budget — that governs release velocity.

## Frameworks Introduced
- **The Error Budget**: `error_budget = 1 − SLO`. As long as budget remains, releases can ship. When exhausted, releases halt. Removes politics from the dev/ops conflict.
  - When to use: Whenever dev and ops fight about release frequency vs. reliability.
  - How: Product Management sets SLO → monitoring measures uptime → gap is the spend-able budget.
- **The 50% Engineering Cap**: SRE ops work (tickets, on-call, manual tasks) must not exceed 50% of time. Excess overflows to dev team.
  - When to use: Continuously. If SREs exceed 50% ops, shift load back to devs.
  - How: Measure ops time quarterly; redirect excess via reassigning bugs/tickets to dev managers.
- **"Hope is not a strategy"**: Traditional SRE motto — every policy decision must be grounded in data and objective metrics, not optimism.

## Key Concepts
- **SRE**: Software engineers who run production systems; bias toward automation over manual operation.
- **Toil cap**: Aggregate ops work ≤ 50%; remainder must be engineering project work.
- **Error budget**: The permitted unreliability per period (1 − SLO). Shared incentive for dev and SRE.
- **Ops/dev split pathology**: Traditional dev/ops divide creates misaligned incentives — devs want velocity, ops want stability. Error budgets dissolve this.
- **Postmortem culture**: Blame-free analysis after significant incidents; goal is system improvement, not blame.
- **Max 2 events per on-call shift**: More than 2 events prevents thorough investigation; fewer than 1 wastes the on-call.
- **Progressive rollouts**: Change management automation: phased rollout → fast detection → safe rollback.
- **MTTF/MTTR**: Reliability = f(mean time to failure, mean time to repair). Playbooks improve MTTR ~3x.

## Mental Models
- Think of error budgets as the "shared currency" of risk — both teams can reason about it without politics.
- Use playbooks for on-call: a practiced engineer with a playbook outperforms a "hero" every time (~3x MTTR improvement).
- The 50% cap is a safety valve, not a target — aim for much less toil long-term.
- 100% availability is wrong for almost every service: users can't distinguish 99.999% from 100%.

## Anti-patterns
- **Heroism without playbooks**: Heroic individual on-call engineers scale poorly and burn out. Systematize the knowledge.
- **Ops team growing linearly with service size**: Without automation, ops headcount scales with traffic. SRE breaks this.
- **Email alerts as monitoring**: Systems that email humans for decisions are fundamentally flawed. Monitoring should page only when human action is needed.
- **Conflict-based release management**: Launch review checklists and "flag flips" workarounds are symptoms of the ops/dev split — error budgets eliminate this.

## Reference Tables
| Monitoring Output | When | Human Action |
|---|---|---|
| Alert | Immediate, action needed now | Page |
| Ticket | Action needed, not urgent | Queue |
| Log | Diagnostic/forensic only | None unless prompted |

## Key Takeaways
1. SRE is what happens when you ask a software engineer to design an operations team — engineering-first mindset applied to operations.
2. Error budgets replace political conflict with objective data: dev and SRE share a single metric for how much risk is acceptable.
3. If ops work exceeds 50%, overflow to dev — this creates feedback pressure to build more reliable, self-managing systems.
4. Playbooks improve MTTR ~3x over "winging it" — always document your on-call procedures.
5. 70% of outages come from changes; progressive rollout + fast detection + safe rollback is the answer.

## Connects To
- **Ch 3**: Error budgets in detail — formulas and quarterly management.
- **Ch 4**: SLOs — what the error budget is measured against.
- **Ch 5**: Toil — the specific definition of the ops work being capped.
- **Ch 6**: Monitoring — the three valid monitoring outputs (alert, ticket, log) are introduced here.
