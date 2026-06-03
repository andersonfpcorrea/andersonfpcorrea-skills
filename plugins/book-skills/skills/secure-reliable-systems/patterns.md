# Patterns — Building Secure and Reliable Systems

---

## Safe Proxy Pattern
**When to use**: Adding logging, MPA, and rate limiting to existing systems without modifying them; transitioning toward Zero Touch Production.
**How**:
1. Deploy a proxy that intercepts all calls to the target system.
2. Configure ACL policies: which roles can invoke which RPCs.
3. Enable MPA for sensitive operations (proxy waits for a second approver).
4. Log all RPCs with caller identity, timestamp, and parameters.
5. Add rate limiting for dangerous operations (e.g., machine restarts).
6. Configure target system to accept calls only from the proxy.
**Trade-offs**: Single point of failure (mitigate with redundant instances); policy misconfiguration risk (use templates/automation for secure defaults); resistance to change (provide breakglass for emergencies).

---

## RPC Interceptor Framework Pattern
**When to use**: Building service frameworks that enforce security and reliability concerns (auth, logging, throttling) for all handlers uniformly.
**How**:
1. Define a pipeline of interceptors: Logging → Authentication → Authorization → Throttling → [RPC Logic].
2. Each interceptor has a `before` and `after` stage.
3. Error in any interceptor's `before` prevents further execution but still runs all `after` stages in reverse.
4. Interceptors share state via a context object.
5. Framework exports metrics automatically (request count, latency, error rate).
**Trade-offs**: Additional abstraction layer; interceptors must not assume execution order within a stage; testing each interceptor independently is critical.

---

## Type-Enforced Security Invariants Pattern
**When to use**: Eliminating whole classes of injection vulnerabilities (SQL injection, XSS) in a codebase.
**How**:
1. Define a type (e.g., `TrustedSqlString`, `SafeHtml`) that can only be constructed by developer-controlled code paths (never from raw user input).
2. Modify security-sensitive APIs to accept only the safe type, not raw strings.
3. The compiler enforces the invariant — no runtime check or code review vigilance needed.
4. Add a migration guide for converting existing code.
**Trade-offs**: Initial migration effort for existing codebase; requires language support for strong typing; can increase API verbosity.

---

## Defense in Depth (Layered Sandboxing) Pattern
**When to use**: Running untrusted code or accepting untrusted inputs in a multi-tenant or high-risk environment.
**How**:
1. Define the threat model: what can the attacker control? What must they not reach?
2. Layer 1: Remove or replace dangerous APIs with safe, purpose-built alternatives.
3. Layer 2: Compile to a sandbox technology (e.g., NaCL, WebAssembly) that prevents memory corruption classes.
4. Layer 3: Apply OS-level sandboxing (ptrace, seccomp-bpf, Linux namespaces) to catch unexpected syscalls.
5. Add monitoring/alerting at each layer to detect and alert on constraint violations.
6. Each layer assumes the previous layer will fail.
**Trade-offs**: Performance overhead; complexity of maintaining multiple layers; each layer must be independently maintained and updated.

---

## Emergency Push = Normal Push Pattern
**When to use**: Designing rollout/recovery systems that can handle both routine releases and emergency security patches.
**How**:
1. Build the rollout system to operate at maximum possible speed.
2. Add a separate, configurable rate-limiting microservice that issues cryptographic tokens approving changes at a given rate.
3. Normal operations: rate limits constrain the rollout to safe, testable speeds.
4. Emergency: adjust or temporarily remove rate limits — same system, same process, same tooling, same confidence.
5. Rate-limiting microservice also serves as the change log for auditing.
**Trade-offs**: Rate-limiting microservice is a dependency; must be designed for high availability; decoupling rate policy from rollout capability requires discipline to not bypass the rate limiter informally.

---

## Access Classification Framework Pattern
**When to use**: Designing a least-privilege access control system for a complex organization with varied data types and operations.
**How**:
1. Define risk tiers (e.g., Public / Sensitive / Highly Sensitive) based on the damage potential if access is misused.
2. For each tier, define read, write, and infrastructure (admin) access controls.
3. Apply controls proportional to tier: low-risk tiers get standard auth; high-risk tiers get MPA, temporary access, and business justifications.
4. Make the classification system programmatic — APIs, data stores, and services declare their tier.
5. Enforce classification through access control systems, not just policy documents.
**Trade-offs**: Classification overhead for every new API/data store; organizations often over-classify (everything is "sensitive") or under-classify (everything is "public").

---

## Binary Provenance + Deployment Policy Pattern
**When to use**: Securing the software supply chain against insider threats and compromised build infrastructure.
**How**:
1. Every CI build generates a signed provenance attestation: source commit, build system, build config, timestamp.
2. The deployment system (CD) verifies the provenance attestation before accepting any deployment.
3. Define a deployment policy: "only artifacts built by CI from reviewed source, signed with key X, within 30 days, may be deployed to production."
4. Add a deployment choke point: a single gateway through which all deployments must pass; block direct deployments to production.
5. Post-deployment: verify artifacts in production match expected provenance.
**Trade-offs**: Build system becomes a high-value attack target; signing key management requires HSM + access controls; provenance chain must cover every dependency, not just the top-level artifact.

---

## Immutable Container Rebuild Pattern
**When to use**: Patching security vulnerabilities in containerized infrastructure.
**How**:
1. Never SSH into running containers to apply patches — containers are immutable.
2. When a vulnerability is discovered: identify all images using the affected dependency (via content-addressable registry).
3. Rebuild images from the patched base image or dependency.
4. Push to registry; deploy using normal release process (canary → staged → full).
5. Enforce maximum container age; automatically flag containers older than a threshold.
**Trade-offs**: Requires a reliable, fast build pipeline; larger images take longer to rebuild; stateful services need careful handling during rebuild cycles.

---

## Disaster Risk Analysis Pattern
**When to use**: Preparing an organization's incident response capability before disasters occur.
**How**:
1. Enumerate all systems; classify as mission-essential, mission-important, or nonessential.
2. For each system, list potential disaster scenarios; rank by probability × impact.
3. Identify resource requirements (human + technological) for each scenario.
4. Prioritize preparedness investment by highest risk-score items.
5. Review and update the analysis periodically (operating environment changes).
**Trade-offs**: Risk analysis is a point-in-time snapshot; must be re-run when the system or threat landscape changes; groupthink can cause teams to miss obvious risks — use external reviewers.

---

## Blameless Postmortem Pattern
**When to use**: After any significant incident (reliability outage or security incident).
**How**:
1. Assemble participants: everyone involved in the incident + at least one person who wasn't.
2. Construct a timeline of events — what happened, when, who acted, what the effects were.
3. Ask "five whys" for each contributing factor — find the systemic root cause, not the human who made a mistake.
4. Generate action items targeting systemic improvements, not individual punishment.
5. Track action items to completion with owners and deadlines.
6. Share postmortems broadly (within the organization) — others learn from incidents they didn't experience.
**Trade-offs**: Requires psychological safety to be effective; management must model blamelessness; "blameless" does not mean "consequence-free" for genuinely negligent behavior.
