# Chapter 6: Design for Understandability

## Core Idea
A system is understandable to the extent that a person with relevant technical background can accurately and confidently reason about its operational behavior and its invariants — understandability is the design property that enables confident security and reliability assertions.

## Frameworks Introduced
- **System Invariants**: Properties that must hold for *all* possible system behaviors, including adversarial inputs. "Only authenticated and authorized users can access the data store" is an invariant if and only if no execution path violates it. Testing alone cannot prove invariants — only principled reasoning about the design can.
- **Mental Models**: Engineers build simplified models of subsystem behavior. Good design ensures mental models remain predictive under unusual conditions (load, attack, failure). Disable virtual memory swap in production: predictable OOM errors, not unpredictable thrashing.
- **Layered Architecture for Security**: Centralize identity, authentication, and authorization in standardized subsystems. Each service calls a shared auth layer; security properties are enforced in one place, not scattered across every service.
- **Security Boundaries**: Explicit demarcation of trust. Two components inside the same trust boundary can communicate directly; crossing a boundary requires authentication + authorization. Reduce the number of boundaries to minimize the attack surface.

## Key Concepts
- **Complexity vs. Understandability**: Inherent feature complexity (Gmail's many features) cannot be eliminated, but must be *managed* so security/reliability properties remain reasonably analyzable. Compartmentalize complexity; don't let it spread across the whole system.
- **Centralized responsibility**: Security and reliability requirements (auth, logging, rate limiting) should be implemented once in shared frameworks, not individually per service. Per-service implementations produce varied bugs and inconsistent enforcement.
- **Understandable interface specifications**: APIs must be designed so their security properties are obvious. A function that accepts `string` SQL query fragments is dangerous; a function that only accepts `TrustedSqlString` (a type the system constructs, not user input) makes the invariant visible.
- **Application frameworks for service-wide requirements**: Frameworks inject auth, logging, and throttling automatically for every service handler. Developers implement business logic; security invariants are enforced by the framework.
- **Data flow analysis**: Understanding how external inputs flow through the system to security-sensitive sinks (SQL queries, HTML output, file writes) enables identifying and eliminating injection vulnerability paths.
- **Tink**: Google's open source cryptographic framework — a concrete example of a "secure by default" API where it is difficult to misuse cryptography.

## Mental Models
- Understandable system → security invariants can be argued informally but confidently → "for all inputs, access check X is always performed" is provably true by inspection, not just tested.
- "Absence of evidence is not evidence of absence" — testing exercised the system for a fraction of possible behaviors; SQL injection tops vulnerability lists because testing alone doesn't prove the invariant holds.
- Design components so their mental models remain correct under adversarial conditions: configure services to fail fast and predictably rather than degrade in confusing ways.

## Anti-patterns
- **Scattered authentication**: Auth logic re-implemented per service leads to inconsistencies; one implementation has a bug = one service exploitable.
- **Implicit trust within a network segment**: "All traffic from our internal network is trusted" is not an invariant — it's a perimeter assumption that breaks when any internal system is compromised.
- **Complex data flows**: When it's unclear which function arguments contain user input vs. developer-controlled data, injection vulnerabilities become untrackable by humans and code review tools.

## Key Takeaways
1. Design for invariants: before writing code, define the security properties that must hold for all inputs, then design the system so they're structurally enforced, not just tested.
2. Centralize identity, auth, and access control in shared subsystems — services should call the framework, not implement their own auth.
3. Application frameworks that inject security behaviors (logging, auth, throttling) at the RPC handler level eliminate entire vulnerability classes across all services.
4. Mental models must remain useful under adversarial and failure conditions — design predictable failure modes, not just happy-path behavior.
5. Use types to make security invariants visible: `TrustedSqlString` makes "this value is SQL-safe" auditable at a glance.

## Connects To
- **Ch 5**: Least privilege + auth framework (understandable identity)
- **Ch 12**: Code-level security frameworks (TrustedSqlString, SafeHtml)
- **Ch 13**: Testing — formal methods and fuzzing to verify invariants beyond unit tests
- **Ch 4**: Understandability as a design investment (sustained velocity)
