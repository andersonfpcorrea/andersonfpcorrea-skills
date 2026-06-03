# Chapter 7: The Evolution of Automation at Google

## Core Idea
Automation is a force multiplier, not a panacea. The ultimate goal is systems that need no automation at all — self-managing by design. The hierarchy of automation classes describes the evolution from manual ops to autonomous systems.

## Frameworks Introduced
- **Hierarchy of Automation Classes** (five levels, ascending desirability):
  1. No automation — manual operations (e.g., manual MySQL failover)
  2. Externally maintained system-specific automation — SRE's personal failover script
  3. Externally maintained generic automation — shared failover script supporting multiple systems
  4. Internally maintained system-specific automation — database ships with its own failover script
  5. Systems that don't need automation — database detects problems and fails over automatically

- **Automation value hierarchy** (reasons, ranked by author's view of importance):
  1. Consistency (humans are inconsistent across repetitions)
  2. Platform (centralizes bugs, extends to more systems, enables metrics)
  3. Faster repairs (reduces MTTR for common faults)
  4. Faster action (machines react faster than humans for well-scoped scenarios)
  5. Time saving (decouples operator from operation — savings accrue across all users)

## Key Concepts
- **Bit rot problem**: Automation maintained separately from the core system diverges as the system changes. Tightly coupling turnup automation to the system requires developer cooperation — difficult to achieve.
- **Infrequent automation is fragile**: Failovers run once per few months; inconsistencies accumulate. The extended feedback cycle prevents discovery of failures until an emergency.
- **MySQL on Borg (MoB) case study**:
  - Problem: Borg tasks move 1–2×/week; manual failover took 30–90 min; could not meet 30s downtime budget.
  - Solution: "Decider" automated failover daemon → 30s failover in 95% of cases.
  - Result: Ops time dropped 95%; hardware utilization improved 60% through bin-packing.
- **Cluster turnup automation evolution**: Started as ~200-step manual process handed to new hires. Evolved over years; mid-way automation introduced subtle bugs (human steps interspersed) that caused prod outages. Final resolution: fully automated turnup that could be triggered by one person.

## Mental Models
- "Decoupling operator from operation is very powerful." — Once automated, any engineer can run the task.
- "Platform over procedure" — A platform that other automation can build on is worth far more than automating one specific task.
- Automation's most important effect: bugs are fixed once and fixed everywhere, unlike humans who repeat the same errors.
- "If we are engineering processes and solutions that are not automatable, we continue having to staff humans to maintain the system." (Joseph Bironas)

## Anti-patterns
- **Automation as a crutch for bad design**: Better than automation is a system designed to not need it. Always ask: can the system handle this condition itself?
- **Half-automated processes**: Interspersing manual steps in automation creates the worst of both worlds — human inconsistency combined with machine speed of failure.
- **Automation that bit-rots**: Infrequently-exercised automation decays. Cluster failover that runs once every few months may be broken when needed.
- **Tool-first automation**: Picking Puppet/Chef/Perl before understanding the problem domain. Higher-level abstractions fail systematically through "leaky abstractions."

## Key Takeaways
1. Target level 5 — systems that self-manage without external automation. Work up the hierarchy from where you are.
2. Consistency is automation's most undervalued benefit — humans performing a task 100 times will be inconsistent; machines won't.
3. Automation that's rarely exercised is usually broken. Test failover procedures; exercise them regularly.
4. Half-automated processes are dangerous — full automation or full manual, not both mixed.
5. The Ads MySQL story: 95% reduction in ops time, 60% hardware savings, by fully automating failover. The investment was worth it.

## Connects To
- **Ch 5**: Eliminating toil — automation is the engineering tool for toil elimination.
- **Ch 8**: Release engineering — automation in the context of safe deployments.
- **Ch 27**: Reliable launches — automation for launch coordination.
