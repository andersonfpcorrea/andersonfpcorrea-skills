# Chapter 12: Non-Abstract Large System Design (NALSD)

## Core Idea
NALSD is an iterative design methodology that starts with single-machine feasibility, adds distribution only when needed, and explicitly evaluates failure modes at each step — producing reliable, cost-aware designs.

## Frameworks Introduced

### NALSD Process
1. **State requirements explicitly**: what must the system do? What SLOs must it meet?
2. **Start with one machine**: can a single machine handle this? What are its limits? (This forces concrete capacity math.)
3. **Identify the first scaling bottleneck**: what breaks first? (CPU, memory, storage, network, single point of failure?)
4. **Add distribution to fix the bottleneck**: partition, replicate, or shard the limiting resource.
5. **Repeat**: each solution introduces new bottlenecks; iterate until the design handles the load with acceptable failure modes.
6. **Evaluate failure modes**: for each component, what happens when it fails? Is the degradation graceful?

### Design Evaluation Criteria (per iteration)
- **Is it feasible?** Can the proposed design handle the stated load with available hardware?
- **Is it correct?** Does it produce the right answer under all conditions?
- **Is it scalable?** What's the next bottleneck, and how far away is it?
- **Is it fault-tolerant?** What failures does it handle? What's the blast radius of each failure mode?
- **Is it operable?** Can a team of N SREs run this without constant manual intervention?
- **What does it cost?** Resource costs, operational costs, and engineering costs.

## Worked Example: AdWords Backend

### One-machine design
- Single server stores all AdWords data in memory; handles all queries.
- Feasible for small datasets; fails when data exceeds machine memory.
- First bottleneck: memory capacity.

### Horizontal partition (sharding)
- Split data across N servers by advertiser ID.
- Fixes memory bottleneck; introduces: where does the query router live? What if a shard is down?
- New bottleneck: single query router is a SPOF.

### Distributed query router
- Run multiple query routers; use consistent hashing for shard assignment.
- Fixes SPOF; introduces: how does a new router learn the shard map?
- New bottleneck: shard map consistency.

**Pattern**: each iteration reveals one new constraint; NALSD makes this structured and visible.

## Key Concepts
- **Non-abstract**: use real numbers (QPS, bytes, latency budgets) at every step; "it'll scale" is not an answer.
- **Capacity planning as design**: resource requirements emerge from the design, not post-hoc.
- **Graceful degradation**: a well-designed NALSD system degrades predictably; a poorly designed one fails catastrophically.
- **SRE as capacity/design partner**: SREs provide the production reality check on design proposals.

## Mental Model: Reliability Tiers
Design each component to handle one tier below the system's overall SLO:
- System SLO 99.9%: each redundant path should handle ~99% independently.
- System SLO 99.99%: each path needs ~99.9%; redundancy gives the system-level target.

## Anti-patterns
- **Designing at abstraction**: "it's distributed" without capacity math doesn't reveal bottlenecks.
- **Replication as the first answer**: replication adds cost and consistency complexity; verify it's necessary before adding it.
- **Deferring failure-mode analysis**: "we'll handle failures later" is how you get a design that fails catastrophically when a component goes down.

## Key Takeaways
1. Start with one machine; add complexity only when the design hits a real bottleneck.
2. Use real numbers at every step: QPS, bytes, latency, machine count.
3. Evaluate failure modes at each iteration; identify graceful degradation paths.
4. NALSD produces cost-aware designs by making resource use explicit throughout.

## Connects To
- **Ch 11**: Load management concepts (admission control, sharding) that NALSD designs naturally incorporate
- **Ch 2**: SLO targets are the design inputs for NALSD
