# Chapter 9: Design for Recovery

## Core Idea
Systems must be designed with recovery as a first-class goal — because hardware, humans, software, and adversaries all introduce failures — and the mechanisms for fast recovery must be the same as normal operations, exercised regularly, and guarded by explicit policy controls on rate of change.

## Frameworks Introduced
- **Four Failure Categories**: (1) Random errors (hardware physics), (2) Accidental errors (well-intentioned human mistakes), (3) Software errors (bugs — delayed accidental errors), (4) Malicious actions (deliberate subversion). Recovery design must address all four without knowing in advance which is occurring.
- **Design to Go Fast, Guarded by Policy**: Build rollout/recovery systems to operate at maximum possible speed; then add rate-limiting controls as a separate, configurable policy layer. Decoupling speed capability from speed policy means you can accelerate in an emergency without redesigning the system.
- **Rate-Limiting as Microservice**: Implement rate limiting as an independent, single-purpose microservice that issues short-lived cryptographic tokens approving changes at a given time. This makes rate-limiting a clear seam, auditable, and hard to accidentally bypass.
- **Revocation Mechanism**: Systems must have explicit, reliable mechanisms to revoke credentials, certificates, and access — not just let them expire. Test revocation pathways regularly; untested revocation often fails during the incident when you need it most.
- **Know Your Intended State**: The rollout/recovery system must have a reliable representation of every component's current AND desired state, down to the bytes. Rollback to "last known good" requires knowing what "good" is and being able to validate the current state against it.

## Key Concepts
- **Rollback security/reliability tradeoff**: Rolling back a broken release may reintroduce security vulnerabilities that were patched in that release. Every rollback decision requires a conscious tradeoff — is the reliability risk worse or better than the security risk?
- **Hermetic tests**: Tests must produce the same result in isolation, every time. Non-hermetic tests cannot be relied upon to validate recovery procedures.
- **Emergency push = regular push at max rate**: Your emergency rollout system should literally be your normal rollout system with rate limits removed. If your emergency system is a separate, rarely exercised system, it will fail during an actual emergency.
- **Explicit time dependencies**: Systems must be able to function with external clock sources unavailable or untrustworthy. Design-in time independence for recovery paths.
- **Emergency access design**: Maintain documented, regularly tested emergency access paths (credentials, contacts, procedures) that are accessible even when normal systems are compromised.
- **ShellShock / Heartbleed**: Real examples of security vulnerabilities requiring fast, broad fleet updates — the organizations with maximal-speed rollout infrastructure won the race against exploiters.

## Anti-patterns
- **Emergency system separate from normal system**: Two systems = two maintenance burdens + one untested system. Merge them: emergency = normal at max speed.
- **Implicit time dependencies in recovery paths**: Systems that call out to time services, certificate validation endpoints, or other external dependencies during recovery may fail when those dependencies are themselves compromised.
- **Rollback without security analysis**: Always ask: "What vulnerabilities does the version we're rolling back to have?" before executing a rollback under pressure.
- **Untested revocation**: Certificate revocation lists and credential revocation pathways are critical but rarely exercised; they often fail when needed.

## Key Takeaways
1. Design rollout/recovery systems for maximum possible speed, then add configurable rate-limit policy — decouple capability from policy so emergencies only require unlocking speed, not redesigning the system.
2. Rollback is a reliability/security tradeoff, not a free undo button — always evaluate the security posture of the version you're returning to.
3. Implement rate limiting as a standalone cryptographic microservice — makes auditing natural and bypassing intentionally difficult.
4. Know your intended state at all times — recovery requires being able to compare current reality to desired state and push toward desired state confidently.
5. Test every recovery pathway as regularly as normal operations — untested recovery fails when needed most.

## Connects To
- **Ch 7**: Fast, frequent releases as recovery infrastructure
- **Ch 8**: Resilience — design that delays the need for recovery
- **Ch 17**: Crisis management — managing recovery under adversarial conditions
- **Ch 18**: Recovery and aftermath — executing recovery after an incident
