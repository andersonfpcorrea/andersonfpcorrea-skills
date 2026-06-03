# Chapter 14: Configuration Design and Best Practices

## Core Idea
Configuration is a human-computer interface; bad configuration design causes more incidents than bad code. The ideal is minimal required configuration with good defaults, questions close to user goals, and change tracking identical to code.

## Frameworks Introduced

### Configuration Philosophy (language-independent)
1. **Configuration asks questions**: every config file/UI is asking the user questions. Minimize the number of mandatory questions; provide safe defaults for optional ones.
2. **Questions close to user goals**: "deploy 4 replicas" is infrastructure-centric; "serve 100 QPS with 99.9% availability" is user-centric. Prefer user-centric.
3. **Escape hatches**: simple defaults for 90% of users; explicit override for the remaining 10%. Don't sacrifice the 90% to satisfy edge cases.
4. **Separate configuration evaluation from side effects**: config that has side effects when evaluated (like writing files) is hard to validate safely.

### Configuration Mechanics (implementation)
1. Separate configuration from resulting data: config is the intent; generated data is the output. Don't version-control generated data.
2. Tooling is mandatory: humans cannot safely make complex configuration changes without tooling that validates, diffs, and applies atomically.
3. Ownership and change tracking: every config change is attributed; config history is queryable.
4. Safe application: config changes are applied gradually (not all at once), with rollback capability.

## Key Concepts
- **Configuration vs. code**: code changes go through review + build + test; config changes can bypass all of this and have immediate effect. This makes config changes disproportionately dangerous.
- **Reliability impact**: a bad firewall rule can lock you out of your system in one line; same can't happen with one line of application code.
- **Cognitive load in emergencies**: configuration systems must be usable under incident pressure. Complex config syntax is a reliability risk.
- **Dynamic defaults**: default values derived from the deployment context (thread count = CPU cores; heap = available memory) reduce mandatory questions without sacrificing correctness.

## Configuration Design Checklist
- [ ] Mandatory questions minimized; safe defaults for optional ones
- [ ] Questions framed in user terms, not infrastructure terms
- [ ] Config changes tracked with attribution (who, when, what)
- [ ] Validation tooling prevents invalid configs from being applied
- [ ] Rollback is possible for every config change
- [ ] Config application is gradual, not instantaneous
- [ ] Generated data is separate from configuration source

## Anti-patterns
- **Infrastructure-centric config**: maximum tunables exposed; users drown in options they don't understand.
- **Config without history**: no audit trail means you can't answer "what changed before this incident?"
- **Big-bang config changes**: applying a new config to all instances simultaneously; one bad line takes down everything.
- **Evaluating config with side effects**: config that creates files, writes to DBs, or contacts external services when loaded is untestable and dangerous.

## Key Takeaways
1. Configuration is a user interface; apply UX principles (minimize required inputs, good defaults, clear error messages).
2. Every config change must have attribution, be validated before application, and be rollback-capable.
3. Separate config intent from generated artifacts; don't version-control generated files.
4. Safe config application = gradual rollout, not instantaneous global change.

## Connects To
- **Ch 15**: Configuration mechanics in practice (Jsonnet, Kubernetes config, pitfalls)
- **Ch 16**: Canarying releases applies the same gradual-rollout principle to binary deployments
