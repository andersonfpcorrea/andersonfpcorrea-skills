# Chapter 7: Simplicity

## Core Idea
Simplicity strongly correlates with reliability. SREs, with their end-to-end system view, are the natural champions for simplicity across the stack — from code to architecture to processes.

## Frameworks Introduced
- **Complexity proxies** (when you can't measure system complexity directly):
  - Training time: how long until a new engineer can go on-call?
  - Explanation time: whiteboard time for a full system overview
  - Administrative diversity: how many ways to configure similar settings?
  - Deployment diversity: how many unique configs are deployed in production?
  - Age: older = more Hyrum's Law accumulation (every behavior becomes depended upon)

- **System complexity smells**:
  - Amplification: a retry at one layer multiplies RPCs at lower layers
  - Cyclic dependencies: cold-start becomes impossible
  - Redundant lookups in a single request: "system smell" analogous to code duplication

## Key Concepts
- **Gall's Law**: "A complex system that works is invariably found to have evolved from a simple system that worked." (Corollary: don't design complex systems from scratch.)
- **Hyrum's Law**: Over time, all observable behaviors of an API become depended upon; APIs cannot evolve freely regardless of what the contract says.
- **Complexity as externality**: the team that introduces complexity rarely pays its full cost; SREs and future maintainers pay it.
- **Simplification as feature**: reserve explicit time (e.g., 10% of eng project time) for simplification; treat it as a deliverable, not overhead.

## Mental Models
- "Would I rather fix a simple or a complex system at 3am?" — use this to evaluate architectural proposals.
- Diagramming exercise: before going on-call, draw the system; keep canonical diagrams updated. Diagrams reveal cyclic deps and amplification paths.
- "Zombie Code Slayer": recognize and celebrate significant code deletion — lines are spent, not produced.

## Anti-patterns
- **Big-bang rewrites**: Borg → Omega failed because Omega chased a moving target, migration cost was underestimated, and both systems had to coexist for years.
- **Abstract APIs**: key/value bags feel simple to write but push complexity onto callers; structured types (Protobuf, Thrift) are simpler end-to-end.
- **Splitting SRE teams to cope with complexity**: reduces the end-to-end view needed to drive simplification.

## Lessons from Case Studies
- **Display Ads Spiderweb**: redundant lookups across independently developed products; fixed by creating a unified auction server with standardized data-access patterns.
- **Shared Microservices Platform**: SRE-driven shared platform reduced unique production stacks; unexpected benefit: developer teams ran hundreds of services without deep SRE engagement.
- **pDNS circular dependency**: pDNS depended on Svelte which depended on pDNS; fixed by caching Svelte IPs locally. Cold start would have been impossible.

## Key Takeaways
1. Simple systems break less often and are faster to fix when they do.
2. SREs, uniquely positioned to see the full stack, should own the end-to-end simplicity mandate.
3. Measure complexity with proxies (training time, explanation time, config diversity).
4. Reserve explicit time for simplification projects; celebrate them as feature launches.

## Connects To
- **Ch 14/15**: Configuration complexity is a major simplicity battleground
- **Ch 12**: NALSD design methodology produces simpler systems by iterating from small
