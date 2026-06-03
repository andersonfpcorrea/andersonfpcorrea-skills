# Chapter 15: Configuration Specifics

## Core Idea
Configuration languages evolve from data formats through accretion of features; the result is usually worse than a purpose-built DSL. Five pitfalls to avoid, and a practical Jsonnet-based approach to reducing configuration toil.

## Frameworks Introduced

### Two Types of Configuration Toil
- **Replication toil**: same values duplicated across many config files; changes require touching N files.
- **Complexity toil**: emergent behavior from complex automation; hard to predict what a change does.

Both types grow with system size; address replication toil before complexity toil, because complexity toil is harder to reverse.

### Five Configuration System Pitfalls
1. **Not treating config as a programming language**: data formats (JSON/YAML) accumulate programming features (loops, interpolation) through the back door → untoolable mess.
2. **Ad hoc language features**: adding features incrementally without design → gotchas and undocumented interactions.
3. **Too much domain-specific optimization**: niche languages have no tooling ecosystem; engineers resist learning them.
4. **Interleaving evaluation with side effects**: config that contacts DNS, allocates VMs, or writes files when *evaluated* is unhermetic — rollback is impossible.
5. **Using a general-purpose scripting language (Python/Ruby/Lua)**: heavyweight; security requires sandboxing; cognitive load for non-programmers.

### Recommended Approach: Use an Existing Config DSL
Candidates: Jsonnet, Dhall, HOCON, CUE. All are hermetic, purpose-built, and have tooling ecosystems. Jsonnet is the worked example in this chapter.

## Jsonnet: Key Properties
- Hermetic: evaluation depends only on inputs, never on external state at eval time
- Superset of JSON: any JSON file is valid Jsonnet
- Supports: variables, functions, conditionals, list comprehensions, object orientation
- Outputs JSON: compatible with Kubernetes, Terraform, Prometheus, etc.

## Example: Kubernetes Config with Jsonnet

```jsonnet
// shared/service.libsonnet — reusable base
{
  local service = self,
  name:: error "name is required",
  port:: 8080,
  
  deployment: {
    apiVersion: "apps/v1",
    kind: "Deployment",
    metadata: { name: service.name },
    spec: {
      replicas: 2,
      selector: { matchLabels: { app: service.name } },
      template: {
        metadata: { labels: { app: service.name } },
        spec: { containers: [{ name: service.name, ports: [{ containerPort: service.port }] }] }
      }
    }
  }
}
```

```jsonnet
// prod/myservice.jsonnet — instance
local base = import "shared/service.libsonnet";
base { name:: "myservice", port:: 9090 }
```

This generates the full Kubernetes YAML from a 3-line override file.

## When to Evaluate Configuration
| Timing | Tradeoff |
|--------|----------|
| At check-in (CI) | Errors caught immediately; generated config versioned |
| At build time | Faster feedback; requires build system integration |
| At runtime | Maximum flexibility; errors surface in production (risky) |

**Recommendation**: evaluate at CI/check-in for production config; runtime only for feature flags and experiment configs.

## Effectively Operating a Configuration System
- **Versioning**: config and its generated output must be versioned together.
- **Source control**: all config changes go through the same review process as code.
- **Tooling**: linter + formatter + diff viewer + CI validation before merge.
- **Testing**: unit tests for config templates; integration tests for generated output.

## Guarding Against Abusive Configuration
- Rate-limit how often config can be changed in production.
- Require config changes to go through canary (see Ch 16) before full rollout.
- Validate semantic constraints (e.g., replica count ≥ 2, health check timeout ≥ 1s) in the config toolchain, not in documentation.

## Anti-patterns
- **YAML + Jinja**: looks simple; produces an untoolable two-language mess.
- **Runtime config with side effects**: eliminates the ability to do dry-run validation.
- **Per-team config systems**: multiplies the toil of maintaining the config system itself.

## Key Takeaways
1. Choose an existing config DSL (Jsonnet, Dhall, HOCON); don't evolve a data format into one.
2. Keep config evaluation hermetic: no external calls during evaluation.
3. Evaluate config at check-in time to catch errors before production.
4. Treat config files as code: review, version, lint, and test them.

## Connects To
- **Ch 14**: Configuration philosophy that these mechanics implement
- **Ch 16**: Config changes are released via canary, just like binary changes
