# Chapter 20: SRE Team Lifecycles

## Core Idea
SRE organizations evolve through predictable stages from zero SREs to a mature multi-team structure. Each stage has specific principles and common failure modes. SLOs with consequences are the prerequisite at every stage.

## Frameworks Introduced

### SRE Maturity Stages

**Stage 0: SRE Practices Without SREs**
- Prerequisite: adopt SLOs even before hiring an SRE.
- Principle #1: "SRE needs SLOs with consequences."
- Practice: acknowledge <100% reliability is acceptable; set a target; tie decisions to it.

**Stage 1: Starting an SRE Role (first SRE hire)**
- Find someone with software engineering skills AND production operations interest.
- Place them embedded with the product team initially (not separate ops team).
- Bootstrap by documenting what exists: system diagrams, runbooks, on-call procedures.
- First SRE's job is not to fix production — it's to understand and document it.

**Stage 2: First SRE Team (forming/storming/norming/performing)**
- **Forming**: team is assembled; establish charter, SLO ownership, on-call rotation.
- **Storming**: conflicts about priorities, tool choices, on-call load; healthy if managed.
- **Norming**: working agreements in place; production reviews established; toil being tracked.
- **Performing**: proactive reliability work; SLOs improving; developers voluntarily consulting SRE.

**Stage 3: Multiple SRE Teams**
- Split by: service complexity, geographic location, SRE rollout expansion.
- Shared tooling platform prevents each team from recreating infrastructure.
- "Mission Control" team owns cross-cutting reliability tools.

### Tuckman's Stages Applied to SRE Team Health

| Stage | Signs | Response |
|-------|-------|----------|
| Forming | Unclear scope, tentative on-call setup | Establish explicit charter and SLO ownership |
| Storming | Prioritization conflicts, tool debates | Structured decision process; escalation path |
| Norming | Shared rituals, consistent on-call | Formalize Production Excellence reviews |
| Performing | Proactive work, developers trust SRE | Focus on expansion and mentoring |
| Declining | Attrition, toil growth, morale drop | Diagnose root cause; escalate to management |

### Suggested Practices for Running Many Teams
- **Mission Control**: central team that owns shared tooling and drives cross-team consistency.
- **SRE Exchange**: rotational program where SREs spend time on other teams; spreads knowledge.
- **Training**: formal curriculum for new SREs; reduces bootstrap time from ~6 months to ~3 months.
- **Horizontal Projects**: cross-team projects (e.g., unified alerting platform) that improve all services.
- **SRE Mobility**: intentional rotation prevents knowledge silos.
- **Launch Coordination Engineering (LCE)**: specialist SREs who review all major launches for reliability.

## Key Concepts
- **Distributed SREs**: SREs embedded in product teams with dotted-line to a central SRE org; balances product-familiarity vs. SRE culture maintenance.
- **Production Excellence (ProdEx) Review**: regular leadership review of SRE team health using a consistent rubric; catches overload and drift early.
- **SRE funding model**: SREs funded as infrastructure (capital budget) vs. product headcount (operating budget); affects who controls SRE priorities.

## Anti-patterns
- **SRE as ops rebranded**: no SLOs, no error budgets, no engineering time; burns out SREs and produces no reliability improvement.
- **First SRE doing on-call immediately**: before documentation exists, on-call generates more toil than value; bootstrap first.
- **Multiple teams with duplicate tooling**: each team invents its own alerting, monitoring, and incident tooling; creates maintenance overhead and inconsistent practices.

## Key Takeaways
1. SLOs with consequences are the prerequisite for SRE at every stage of maturity.
2. First SRE's priority: understand and document the system; don't immediately take on-call.
3. Shared tooling platforms scale SRE impact; each team rebuilding the wheel does not.
4. ProdEx Reviews give leadership visibility into team health before burnout sets in.

## Connects To
- **Ch 18**: SRE engagement model — how teams decide what to support
- **Ch 17**: Team overload — the failure mode when lifecycle management is ignored
- **Ch 21**: Organizational change management — the discipline needed to grow an SRE org
