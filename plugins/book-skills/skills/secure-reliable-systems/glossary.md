# Glossary — Building Secure and Reliable Systems

**Abstract Interpretation** — Static analysis technique that mathematically over-approximates all possible program behaviors to prove properties hold for all inputs. (Ch 13)

**Accidental error** — System failure caused by a well-intentioned human making a mistake. (Ch 9)

**Ambient authority** — Implicit privilege a user has by virtue of their role or network position, without explicit per-action authorization. Least privilege eliminates ambient authority. (Ch 5)

**Amplification attack** — DoS attack where attacker spoofs victim's IP, sends small requests to many servers (DNS, NTP, memcache), which send large responses to the victim. (Ch 10)

**Audit log** — Granular, tamper-evident record of who did what, when, and why. Effective only when using small functional APIs, not interactive sessions. (Ch 5, 15)

**BeyondCorp** — Google's implementation of Zero Trust Networking. Access based on user identity + device credential, not network location. (Ch 5)

**Binary provenance** — Cryptographically verifiable record of where a binary came from: source revision, build system, build configuration. (Ch 14)

**Blast radius** — The extent of impact a single failure or compromise can propagate. Minimized by compartmentalization, role/location/time separation. (Ch 1, 8)

**Blue Team** — Defensive security team; builds and maintains security infrastructure; monitors and responds to threats. (Ch 20)

**Breakglass mechanism** — Emergency bypass of normal authorization controls. Must be rare, logged, audited, and culturally treated as an emergency. (Ch 5, 14)

**Bug bounty (Vulnerability Reward Program)** — Formal program rewarding external researchers who responsibly disclose security vulnerabilities. (Ch 2, 20)

**Business justifications** — Requiring operators to declare a reason for access before it is granted; creates an audit trail and friction against casual misuse. (Ch 5)

**CA/Browser Forum** — Industry consortium that sets baseline requirements for publicly trusted Certificate Authorities. (Ch 11)

**CIA Triad** — Confidentiality, Integrity, Availability — the three fundamental security properties. Also relevant to reliability (accidental violation vs. adversarial). (Ch 1)

**Continuous fuzzing** — Running fuzz testing perpetually in CI/CD, not just during security reviews. (Ch 13)

**Containers** — Immutable, content-addressable deployment units. Patch by rebuilding the image, not modifying live containers. (Ch 7)

**Coverage-guided fuzzing** — Fuzzer instruments the binary to track branch coverage; generates inputs that maximize new code path exploration. (Ch 13)

**Cyber Kill Chain™** — Framework modeling attack stages: reconnaissance, weaponization, delivery, exploitation, installation, C2, exfiltration. Defenders can disrupt at any stage. (Ch 2)

**Defense in depth** — Multiple independent layers of defense, each anticipating the failure modes of the layer before it. (Ch 1, 8)

**DiRT (Disaster Recovery Testing)** — Google's program for regularly simulating internal system failures to exercise incident response. (Ch 1, 16)

**Emergent property** — A system property that arises from the whole system's design, not from any single module. Security and reliability are both emergent. (Ch 1, 4)

**Error budget** — The acceptable failure rate for a service, defined explicitly. When consumed, velocity slows and reliability investment increases. (Ch 1, 21)

**Fail-safe** — System fails to an open/permissive state (door unlocks). Prioritizes reliability of access. (Ch 1)

**Fail-secure** — System fails to a closed/restrictive state (door locks). Prioritizes security. (Ch 1)

**Failure domain** — A compartment of the system that can fail independently of others. Cross-domain blast radius is minimized by design. (Ch 8)

**Feature flag** — A mechanism to enable/disable code behavior at runtime without deployment. Enables staged rollouts with zero behavioral change when disabled. (Ch 7)

**Formal methods** — Mathematical proof that a system has specified properties for all possible inputs. Highly confident but expensive. (Ch 13)

**Fuzz driver** — A small program that accepts arbitrary bytes from a fuzzer and feeds them to a target function. (Ch 13)

**Fuzz testing (fuzzing)** — Automatically generate large numbers of inputs to discover crashes, memory corruption, and unexpected behavior. (Ch 13)

**Graceful degradation** — System sheds features under load in a planned, priority-ordered way rather than failing unpredictably. (Ch 8, 10)

**Hermetic test** — A test that produces the same result in isolation, every time, without external dependencies. (Ch 9, 13)

**HSM (Hardware Security Module)** — Dedicated hardware for generating and storing cryptographic keys; keys cannot be extracted in plaintext. (Ch 11)

**Immutable logging** — Logs that are append-only and tamper-evident; shipped in real time to a separate hardened system. (Ch 15)

**IMAG (Incident Management at Google)** — Google's incident management framework modeled on the US Incident Command System. (Ch 1, 17)

**Incident Commander (IC)** — Single point of authority during an incident; manages the incident, delegates technical work. (Ch 17)

**Insider threat** — Risk from users with legitimate access who act maliciously or negligently. Mitigated by least privilege and MPA. (Ch 2, 5)

**Invariant** — A system property that must hold for all possible behaviors, including adversarial inputs. (Ch 6)

**Least privilege** — Users, automation, and machines receive only the minimum access required for the task at hand. (Ch 1, 5)

**Location separation** — Geographic/zone/datacenter isolation; credentials scoped to a region cannot be used to move laterally across regions. (Ch 8)

**Mental model** — A simplified model of subsystem behavior that engineers use to reason about a system. Must remain predictive under adversarial and failure conditions. (Ch 6)

**Microservices** — Architecture where workloads are split into independently deployable, scalable services. Natural defense in depth; independent patching. (Ch 7)

**MPA (Multi-Party Authorization)** — Requiring a second approver to authorize an action before execution; enforced by the system, not just culture. (Ch 1, 3, 5)

**Mutation testing** — Test quality technique that introduces deliberate bugs and checks whether tests detect them. (Ch 13)

**NaCL (Native Client)** — Sandboxing technology that prevents classes of memory corruption and control-flow subversion attacks. (Ch 8)

**OPSEC (Operational Security)** — Restricting sensitive information to need-to-know parties; during security incidents, prevents tipping off the adversary. (Ch 17)

**Postmortem (blameless)** — Post-incident analysis to identify systemic root causes without assigning personal blame. Enables learning and prevents recurrence. (Ch 21)

**Provenance-based deployment policy** — Deployment system rejects artifacts without valid, verified build provenance. (Ch 14)

**Quarantine** — Isolating compromised systems from the network before rebuilding, to stop attacker pivot and prevent recontamination. (Ch 18)

**Rate limiting** — Constraining the speed at which changes or requests can be processed; a configurable policy separate from the system's maximum capability. (Ch 9, 10)

**Red Team** — Offensive security team that simulates adversaries to find gaps before real attackers do. (Ch 2, 20)

**Role separation** — Different services/users operate under different credentials; compromise of one role cannot reach others. (Ch 8)

**SafeHtml** — A type representing HTML content safe to render; prevents XSS by making unsafe HTML unrepresentable in the type system. (Ch 12)

**Safe Proxy** — A proxy that routes client requests to target systems while enforcing ACLs, MPA, rate limits, and logging. (Ch 3)

**Sandboxing** — Isolating untrusted code/data in a restricted execution environment so a compromise cannot reach the rest of the system. (Ch 8)

**Security boundary** — Explicit demarcation of trust; crossing requires authentication + authorization. (Ch 6)

**Security Champion** — Developer trained in security who serves as first-line security reviewer for their team. (Ch 21)

**Software supply chain** — The process from writing code through VCS → CI build → test → CD deploy; each step is an attack surface. (Ch 14)

**Static analysis** — Analyzing source code without executing it; from linters (pattern matching) to formal verification (mathematical proofs). (Ch 13)

**System invariant** — A property that is always true for all possible system behaviors, including adversarial inputs. (Ch 6)

**3FA (Three-Factor Authorization)** — User identity + device credential + third factor (hardware token, biometric) for highest-risk operations. (Ch 5)

**Time separation** — Time-boxed credentials; operations require re-authorization after a window expires. Limits damage from a compromised credential. (Ch 8)

**Tink** — Google's open source cryptographic framework; a secure-by-default API that is hard to misuse. (Ch 6, 12)

**TrustedSqlString** — A type that can only be constructed from developer-controlled strings; prevents SQL injection by making unsafe queries unrepresentable. (Ch 6, 12)

**TTPs (Tactics, Techniques, Procedures)** — The behavioral fingerprint of attacker groups; enables detection, attribution, and prediction. (Ch 2)

**Verifiable build (hermetic build)** — A build that produces byte-for-byte identical output from the same inputs, enabling third-party verification. (Ch 14)

**Zero Touch Production (ZTP)** — Every production change made by automation, pre-validated by software, or triggered through an audited breakglass mechanism. (Ch 3, 5)

**Zero Trust Networking** — Network location grants no implicit privilege; every access decision verifies user + device identity. (Ch 5)
