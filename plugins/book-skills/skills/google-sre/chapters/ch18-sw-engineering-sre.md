# Chapter 18: Software Engineering in SRE

## Core Idea
SRE teams build production-grade internal software (not quick hacks) because they have unique knowledge of production systems and direct customer relationships with fellow SREs. The Auxon case study demonstrates intent-based capacity planning replacing manual spreadsheet bin-packing.

## Frameworks Introduced
- **Intent-Based Capacity Planning**: Instead of specifying "X cores in cluster Y," encode the *intent* (SLOs, latency requirements, redundancy needs, geographic constraints) and let a solver generate the allocation plan. When inputs change, regenerate — don't manually patch.
  - Key principle: "Specify the requirements, not the implementation."
  - When to use: Any capacity planning problem where requirements > 1 dimension (geography, latency, redundancy, budget).

## Key Concepts
- **SRE software engineering advantages**: (1) Production domain expertise → right design choices for scale/failure; (2) Embedded in problem → understand requirements deeply; (3) Internal users → high-signal feedback, fast iteration.
- **Team growth rule**: "Team size should not scale directly with service growth." SRE software engineering achieves sub-linear team growth against exponential service growth.
- **Traditional capacity planning failure modes**:
  - Brittle: any minor change requires re-doing the entire plan
  - Laborious: spreadsheets, manual bin-packing (NP-hard)
  - Imprecise: simplifying assumptions reduce accuracy; human bin-packing is suboptimal
- **Bin packing as NP-hard problem**: Traditional capacity planning asks humans to solve an NP-hard optimization problem manually. Auxon delegates this to computers, achieving known-optimal solutions.
- **Fostering SRE software engineering**: Use "20% time equivalent" for projects; create product roadmaps even for internal tools; have dedicated PMs for large SRE software projects; measure adoption and customer satisfaction.
- **Career balance**: SRE engineering projects provide career development, prevent skill atrophy, balance on-call/interrupt work, retain engineers who want to code.

## Mental Models
- SRE software is "full-fledged engineering projects, distinct from one-off solutions and quick hacks" — same quality bar as product software.
- Intent → solver → allocation plan. The human input is the *intent*, not the implementation details.
- "People with direct experience running production systems developing the tools that keep production running" — this is why SRE software projects succeed where external tools fail.

## Anti-patterns
- **Spreadsheet capacity planning**: Cannot enforce constraints, cannot auto-replan on change, not version controlled, collaboration is manual.
- **Resource requests without intent**: "X cores in Y cluster" strips out the flexibility and reasoning that enables good bin-packing.
- **One-off scripts vs. products**: Quick hacks for internal tooling compound over time. Treat internal tools as products with customers and roadmaps.

## Key Takeaways
1. SRE software projects succeed because SREs have both production domain expertise and direct customer access — fast feedback, right design.
2. Intent-based capacity planning: encode WHY resources are needed (SLOs, latency, redundancy), let the solver compute HOW to fulfill it.
3. Sub-linear team scaling requires continuous automation investment — software is the leverage.
4. Treat internal tools as products: roadmaps, product managers, customer satisfaction metrics.
5. SRE engineering projects provide career development and reduce on-call burnout — they're not optional.

## Connects To
- **Ch 5**: Toil elimination — Auxon is a case study of eliminating manual capacity planning toil.
- **Ch 7**: Automation — the hierarchy of automation applied to capacity planning.
- **Ch 2**: Google production environment — Auxon manages resource allocation in this environment.
