# Chapter 17: Testing for Reliability

## Core Idea
Tests quantify confidence in a system. Zero MTTR bugs (caught pre-production by tests) are infinitely cheaper than production bugs. Testing is the mechanism that proves equivalence across changes.

## Frameworks Introduced
- **Testing Hierarchy** (traditional, ascending integration scope):
  1. Unit tests — single class/function, milliseconds
  2. Integration tests — assembled components, seconds to minutes
  3. System tests (smoke, performance, regression) — full system, minutes to hours

- **Production Tests** (live system, not hermetic):
  - Rollout tests — verify correctness during staged deployment
  - Configuration tests — verify config matches intent (most common source of production bugs)
  - Stress tests — confirm system breaks gracefully at known limits
  - Canary tests — early-adopter subset of users

- **Zero MTTR principle**: A test that catches a bug pre-production means that bug never reaches users. As MTBF grows from better testing, release velocity increases — tests accelerate development.

## Key Concepts
- **Testing-MTTR relationship**: Failing tests prove absence of reliability; passing tests don't guarantee it, but increase confidence. Tests reduce uncertainty about each change.
- **Rollout entanglement problem**: Production is not hermetic — multiple versions may coexist during rollout. Tests must be run against the actual deployed version, not the latest source.
- **Configuration tests**: Critical in SRE context. Config is pushed separately from binaries; config bugs escape unit/integration tests. Test configs explicitly.
- **Smoke tests**: First line of system tests. Cheap, fast, verify critical paths. Short-circuit expensive tests on clearly broken builds.
- **Performance regression tests**: Systems silently degrade — 10ms → 50ms → 100ms happens incrementally without tests to enforce bounds. Establish performance baselines early.
- **Canary analysis**: Compare live production traffic between old and new version. Any deviation in error rate, latency, or correctness is a stop signal.
- **Probers/black-box tests in production**: Run synthetic transactions against production continuously. Alert when they fail. This is the definition of availability for many services.
- **Test environment cost awareness**: Unit tests are cheap (laptop, milliseconds); full system tests are expensive (dedicated clusters, hours). Budget test resources accordingly.

## Mental Models
- "If you haven't tried it, assume it's broken." — Unknown
- Every test that passes before AND after a change reduces the uncertainty introduced by that change.
- Testing is prediction: "given this test history, how will this system behave in the next N changes?"
- Zero MTTR > low MTTR > any MTTR. Tests in pre-production are infinitely cheaper than production incidents.

## Anti-patterns
- **No performance regression tests**: Systems degrade silently in small increments that each seem acceptable. Only a time-series baseline reveals the drift.
- **Testing only against HEAD**: Rollout entanglement — production may run a different version than source HEAD.
- **Skipping configuration tests**: Most production incidents involve config, not code. Config without tests is untested code.
- **Over-testing in production**: Production probers add load and can cause the incidents they're trying to detect.

## Key Takeaways
1. Zero MTTR bugs (caught pre-production) are free — they never reach users. Invest in test coverage proportional to reliability requirements.
2. Configuration is code — test it explicitly with integration and configuration tests.
3. Performance regressions sneak in incrementally — establish baselines and test against them continuously.
4. Canary analysis (comparing old vs. new on live traffic) catches issues that no hermetic test can find.
5. Test costs scale with scope — unit tests are nearly free; full system tests are expensive. Budget deliberately.

## Connects To
- **Ch 8**: Release engineering — tests gate canary and production deployments.
- **Ch 6**: Monitoring — production probers/black-box tests are the live monitoring layer.
- **Ch 13**: Emergency response — untested rollback procedures fail at the worst moment.
