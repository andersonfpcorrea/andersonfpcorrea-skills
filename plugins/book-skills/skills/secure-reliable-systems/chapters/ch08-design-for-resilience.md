# Chapter 8: Design for Resilience

## Core Idea
Resilience is the system's ability to withstand and delay breakage — through defense in depth, controlled degradation, blast radius limiting, and continuous validation — so that services remain (possibly degraded) even during simultaneous failures.

## Frameworks Introduced
- **Defense in Depth**: Multiple independent layers of defense, each anticipating the failure modes of the layer before it. An attacker (or failure) must breach every layer to compromise the system. The Trojan Horse parable: Troy failed at threat detection, physical inspection, and compartmentalization — all layers failed together.
- **Controlling Degradation**: When a system cannot serve all requests, choose which features to preserve and which to shed. Prioritize by cost of failure × criticality. Pre-plan degradation modes rather than letting the system break unpredictably at its weakest point.
- **Blast Radius Control — Three Separations**:
  1. **Role separation**: Different services/users operate under different credentials; compromise of one role cannot reach others.
  2. **Location separation**: Geographic/zone/datacenter isolation. Credentials scoped to a region prevent lateral movement.
  3. **Time separation**: Time-boxed credentials; operations require re-authorization after a window expires.
- **Failure Domains and Redundancy**: Compartmentalize the system so a failure in one domain cannot cascade to others. Design redundancy within and across domains.
- **Continuous Validation**: Regularly test resilience properties in production (not just pre-launch). Chaos engineering / DiRT exercises verify that automated responses actually work.

## Key Concepts
- **Google App Engine defense-in-depth example**: Removed risky APIs, replaced with safe versions → compiled Python to NaCL bitcode (memory corruption prevention) → added ptrace sandbox layer (catches unexpected syscalls). Each layer anticipates the failure mode of the previous one.
- **NaCL (Native Client)**: A sandboxing technology that prevents whole classes of memory corruption and control-flow subversion attacks.
- **Sandboxing**: Isolating untrusted code/data in a restricted execution environment so a compromise cannot reach the rest of the system.
- **Slashdot effect**: Unexpected traffic spike from viral popularity that mirrors a DoS attack from the system's perspective — design for both.
- **Graceful degradation vs. crashing**: When overloaded, serve overload errors predictably rather than crashing unpredictably. Crashing under load triggers cascading failures; overload errors allow load balancers to route around.
- **Trojan Horse attack stages**: (1) Threat modeling + recon, (2) Deployment/positioning, (3) Execution, (4) Compromise. Defenders can disrupt at every stage before stage 4.

## Reference Table: Resilience Principles vs. Controls
| Principle | Control Mechanisms |
|---|---|
| Defense in depth | Multiple sandbox layers, independent encryption, layered network + app controls |
| Controlled degradation | Feature prioritization, load shedding, graceful error responses |
| Blast radius limiting | Role/location/time separation, scoped credentials |
| Failure domain isolation | Compartmentalized permissions, geographic credential scoping |
| Continuous validation | DiRT exercises, chaos engineering, production load tests |

## Anti-patterns
- **Single defense layer**: Once breached, attacker has full access; each layer should assume the prior layer will fail.
- **Unconstrained degradation**: System breaks unpredictably at its weakest point when overloaded; plan the degradation path explicitly.
- **Overly broad credentials**: Credentials scoped to the entire infrastructure allow lateral movement after any single compromise.

## Key Takeaways
1. Design defense in depth: each layer must be independently resilient and assume the prior layer can fail.
2. Plan degradation paths explicitly — pre-rank features by criticality and decide in advance which to shed under load/attack.
3. Three separations (role, location, time) limit blast radius; apply all three for high-risk operations.
4. Validate resilience continuously in production — untested resilience properties are not reliable.
5. Graceful degradation (overload errors) is vastly preferable to crashing — predictable errors preserve system stability; crashes cascade.

## Connects To
- **Ch 5**: Role separation and credential scoping (least privilege)
- **Ch 9**: Recovery after resilience mechanisms are exhausted
- **Ch 10**: DoS — layered defenses for denial-of-service specifically
- **Ch 1**: Blast radius and defense in depth concepts
