# Chapter 6: Integrating InfoSec into the Delivery Lifecycle

## Core Idea
Building security into daily development work — "shifting left" — improves both delivery performance and security quality; high performers spend 50% less time remediating security issues than low performers.

## Frameworks Introduced
- **Shift Left on Security**: Move security from a downstream gate to an integral part of the entire delivery lifecycle.
  - Security reviews for all major features, conducted without slowing development.
  - InfoSec experts participate in application design, demos, and provide feedback throughout delivery.
  - Security features tested as part of automated test suites.
  - Pre-approved, easy-to-consume security libraries, packages, toolchains, and processes available to developers.

- **Rugged DevOps / DevSecOps**: The principle that security is everyone's responsibility — developers write code that is "rugged" (resilient to adversarial use), not just functional.

## Key Concepts
- **Shifting left**: Moving a concern earlier in the software delivery lifecycle — in this case, integrating security from design onward rather than at the end.
- **Security bottleneck**: Many organizations treat security/compliance as a final gate before production, creating long delays and expensive rework. Upstream integration eliminates this.
- **Staffing ratio context**: ~1 infosec person per 10 infrastructure per 100 developers in large organizations; security teams cannot scale through manual review alone — tooling and developer ownership are required.

## Anti-patterns
- **Security as a downstream gate**: Security review only at "dev complete" creates expensive rework, slows delivery, and is inconsistent at scale.
- **Security knowledge siloed in InfoSec team**: Developers unaware of OWASP Top 10 and common vulnerabilities will introduce security debt continuously; training and pre-approved libraries address this.

## Key Takeaways
1. High performers integrate security throughout; low performers treat it as a phase — and spend 2× as much time remediating issues as a result.
2. When InfoSec provides tooling that makes the secure path the easy path, developers adopt it without enforcement.
3. Shifting left improves both delivery performance (fewer bottlenecks) and security quality (fewer issues to remediate).
4. Compliance requirements (e.g., FISMA/NIST RMF) can be satisfied through deployment pipelines and automated controls rather than slow manual review processes.

## Connects To
- **Ch 4**: Shift left on security is capability #7 in the CD capabilities list; it contributes to CD performance.
- **Ch 5**: Pre-approved security toolchains are a form of "architect for empowered teams."
- **Appendix A**: Capability #7 — Shift left on security.
