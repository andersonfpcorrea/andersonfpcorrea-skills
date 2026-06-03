# Chapter 8: Release Engineering

## Core Idea
Reliable releases require hermetic, reproducible builds and automated deployments. ~70% of outages come from changes — release engineering makes changes predictable and safe.

## Frameworks Introduced
- **Four Release Engineering Principles**:
  1. **Self-Service Model**: Teams control their own release processes; engineering time minimized through automation.
  2. **High Velocity**: Frequent, small releases (fewer changes between versions = easier testing and rollback). "Push on Green" for highest-velocity teams.
  3. **Hermetic Builds**: Identical inputs → identical outputs, regardless of machine or time. Builds depend on known tool versions, not host environment.
  4. **Enforcement of Policies**: Multi-layer access control; every action audited and logged; automatic change reports.

- **Deployment risk profiling**:
  - Dev/pre-production: build hourly, push automatically on green.
  - Large user-facing: start in one cluster, expand exponentially.
  - Sensitive infrastructure: roll out over several days, interleaved across regions.

## Key Concepts
- **Rapid**: Google's automated release system. Blueprints configure build + test + deployment. Dispatches work to Borg jobs, handles thousands of simultaneous releases.
- **Blaze (open source: Bazel)**: Build tool. Supports C++, Java, Python, Go, JS. All builds are hermetic.
- **MPM (Midas Package Manager)**: Distributes software to production; packages versioned by unique hash + signed; labels applied (dev → canary → production).
- **Sisyphus**: General-purpose rollout automation framework; Python extensible; supports any deployment topology.
- **Cherry picking**: Rebuilt at same revision + specific changes submitted after that point. Build tools versioned with the project — last month's build won't accidentally pick up this month's compiler.
- **Configuration management options**: (1) Mainline config (simple, risks skew), (2) config with binary in MPM package (tightly coupled), (3) separate config MPM package (flexible, hermetic), (4) config in external store (Chubby/Bigtable for frequently changing values).
- **Branch and cherry pick**: All code in mainline; release branches never merge back to mainline; cherry picks bring specific fixes to the branch.

## Mental Models
- "Releases can be as painless as simply pressing a button." — The goal is zero friction for routine releases.
- Hermetic = reproducible. If you can't reproduce a build, you can't reproduce the fix for a production bug.
- Fit deployment process to the risk profile — more sensitivity = slower rollout with more checkpoints.
- Release engineering belongs at the start of the product lifecycle, not as an afterthought.

## Anti-patterns
- **Configuration separate from binary lifecycle**: Config changes that are invisible to the release process are a major source of "unexplained" production incidents.
- **Manual canary assessment**: Any manual step in the rollout is a potential single point of failure. Automate go/no-go signals.
- **Release engineering as afterthought**: "Teams should budget for release engineering resources at the beginning." Retrofitting is expensive.
- **Unique snowflake builds**: If you can't reproduce a build exactly, you cannot safely roll back or patch.

## Key Takeaways
1. Hermetic builds eliminate "works on my machine" — identical inputs must always produce identical outputs.
2. Frequent small releases are safer than infrequent large ones: fewer changes = easier debugging = faster rollback.
3. All configuration changes should go through the same release process as code changes — config is code.
4. Match rollout speed to risk: hourly pushes for dev, exponential expansion for user-facing, multi-day interleaved for critical infrastructure.
5. Every release should produce an auditable report of exactly what changed — essential for troubleshooting.

## Connects To
- **Ch 7**: Automation — Rapid, Sisyphus, and Blaze are all automation platforms.
- **Ch 13**: Emergency response — change-induced emergencies are the most common incident type.
- **Ch 22**: Cascading failures — bad releases are a primary trigger.
