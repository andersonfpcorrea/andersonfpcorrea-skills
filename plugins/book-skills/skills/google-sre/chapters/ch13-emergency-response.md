# Chapter 13: Emergency Response

## Core Idea
Emergency response is a practiced skill, not an innate talent. Three types of emergencies (test-induced, change-induced, process-induced) teach the same lesson: preparation, rollback procedures, and communication matter more than heroics.

## Frameworks Introduced
- **Three Emergency Categories** (with distinct preparation needs):
  1. **Test-induced**: Controlled failures exposing unexpected dependencies. Can get out of hand when scope assumptions are wrong.
  2. **Change-induced**: Configuration/code pushes that trigger bugs. Friday afternoon is the canonical danger zone. ~70% of outages.
  3. **Process-induced**: Automation acting faster than humans can comprehend or halt. Efficiency of automation becomes a risk.

- **Emergency response principles** (from case studies):
  1. Abort the test/rollback the change at the first sign of unexpected broad impact
  2. Use parallel mitigation paths — fix the root cause AND work around it simultaneously
  3. Out-of-band communication systems must be maintained independently of production infrastructure
  4. Test rollback procedures before you need them — untested rollbacks are broken rollbacks

## Key Concepts
- **Panic rooms**: Dedicated secure rooms with backup access to production, used when corporate network is down. Production access must be independent of the services being fixed.
- **Out-of-band communication**: If your troubleshooting tools run on the systems you're troubleshooting, you lose both. Maintain low-overhead, high-reliability backup comms (IRC, SMS, dedicated hardware).
- **Rate-limiting as unintentional protection**: During the change-induced emergency, Google's infrastructure rate-limited config distribution, accidentally preventing a full simultaneous crash-loop. Don't rely on this — design explicit blast radius controls.
- **Rollback procedure testing**: If you haven't tested rollback in a test environment, assume it's broken. The case study found this when rollback failed during an actual emergency.
- **Canary process scope**: A change that doesn't exercise a specific keyword combination in canary will not catch a bug triggered by that combination in production. Canary scope must match prod variation.
- **Process-induced automation risk**: Automation can submit multiple conflicting requests simultaneously. Rate-limit automation workflows; add human approval for high-impact operations.

## Mental Models
- "Don't panic" — professional calm is itself a skill. You are trained for this; physical danger is not present.
- Every emergency is a learning opportunity. "All problems have solutions" — if you haven't found it, you haven't looked hard enough.
- Quick push engineer luck saved the change-induced outage — never rely on luck. Build detection and auto-rollback.
- "Learn from the past. Don't repeat it." — postmortems exist for this reason.

## Anti-patterns
- **Assuming canary scope covers prod**: Any configuration keyword not in the canary workload won't be tested.
- **Untested rollback procedures**: Rollbacks are procedures too; they break without testing.
- **Depending on production tools for production debugging**: When prod is down, tools on prod are down.
- **Trusting automation without rate limiting**: Automation submitting conflicting high-impact operations is a common disaster pattern.

## Key Takeaways
1. ~70% of outages are change-induced — canary every change, test rollback before you need it.
2. Test-induced emergencies reveal hidden dependencies; they are valuable precisely because they catch surprises before users do.
3. Out-of-band communication and access tools must be maintained independently — they're useless if they run on the stack you're trying to fix.
4. Automation acting faster than humans is a process-induced emergency waiting to happen — rate limit, add approval gates.
5. Parallel mitigation: fix the cause AND work around it simultaneously to minimize user impact duration.

## Connects To
- **Ch 8**: Release engineering — hermetic builds and canary deployment reduce change-induced emergencies.
- **Ch 14**: Managing incidents — the structured response applied to these cases.
- **Ch 15**: Postmortems — each case study should become one.
- **Ch 22**: Cascading failures — process-induced emergencies often become cascading.
