---
name: secure-reliable-systems
description: "Knowledge base from \"Building Secure and Reliable Systems\" by Heather Adkins et al. (Google). Use when applying its frameworks for the security×reliability intersection, designing for least privilege, understandability, resilience, and recovery, mitigating DoS, securing the software supply chain, or running crisis management. Lane: where security and reliability meet — designing, implementing, and maintaining systems against both accidents and adversaries."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, framework, principle, or chapter number — e.g. 'least privilege', 'blast radius', 'supply chain', 'ch17']"
---

# Building Secure and Reliable Systems
**Authors**: Heather Adkins, Betsy Beyer, Paul Blankinship, Piotr Lewandowski, Ana Oprea, Adam Stubblefield (Google) | **Pages**: ~557 | **Chapters**: 21 | **Generated**: 2026-06-03

## How to Use This Skill

- **Without arguments** — load the core security×reliability frameworks below
- **With a topic** — ask about `least privilege`, `DoS`, `recovery`, `crisis management`; I find and read the relevant chapter
- **With a chapter** — ask for `ch08`; I load that specific chapter
- **Browse** — ask "what chapters do you have?" for the full index

When you ask about a topic not in Core Frameworks below, I read the relevant chapter file before answering. Pairs with `google-sre`, `sre-workbook`, `hidalgo-slo`, and `observability-engineering`.

---

## Core Frameworks & Mental Models

### 1. The Central Thesis — Reliability and Security Are the Same Problem, Minus/Plus an Adversary
Both are **emergent properties** of system design; neither can be bolted on later. They share six commonalities (invisibility, assessment, simplicity, evolution, resilience, response/recovery). The one difference drives everything: **reliability assumes nonmalicious failures; security assumes an active adversary** who makes worst-case events correlated and intentional.
- "When designing for reliability, assume some things will go wrong. When designing for security, assume an adversary is *trying* to make things go wrong."

### 2. The Core Tension — Fail-Safe vs. Fail-Secure
Reliability tends to **fail open** (door unlocks on power loss → safe exit); security tends to **fail closed** (door stays locked → no exploitation). **Redundancy improves reliability but always widens the attack surface.** Every design choice must be checked against *both* threat models — see the decision table in [cheatsheet.md](cheatsheet.md).

### 3. The Four Design-For Principles (the spine of the book)
1. **Design for Least Privilege** (Ch 5) — minimum access for the task. Mechanisms: small functional APIs, **Multi-Party Authorization (MPA)**, **Zero Trust**, temporary/just-in-time access, breakglass. Classify access by risk (see cheatsheet).
2. **Design for Understandability** (Ch 6) — you must be able to reason about **system invariants**. Centralize authentication/authorization, use type-safe APIs to make insecure states unrepresentable, keep designs simple enough to analyze.
3. **Design for Resilience** (Ch 8) — **defense in depth** (each layer assumes the previous failed), **blast-radius** control via compartmentalization and distinct failure domains, **controlled/graceful degradation** under attack or load.
4. **Design for Recovery** (Ch 9) — return to a known-good state fast. **Fast rollout = fast recovery**; make state explicit; rebuild from known-clean images rather than patching in place.

### 4. Least Privilege Toolkit
Least privilege is the book's most load-bearing idea. Apply controls proportional to risk: standard auth (low) → RBAC + audit (medium) → **MPA + justification + temporary access** (high) → 3FA + MPA + HSM + breakglass-only (critical). The **Safe Proxy** pattern (Ch 3) mediates privileged operations through an auditable, policy-enforcing choke point.

### 5. Secure the Software Supply Chain (Ch 14)
Treat **config changes like code changes**. Builds only from version control; CI **generates and signs build provenance**; the deployment system **verifies provenance before deploying**; a **deployment choke point** blocks direct production access. Make the **emergency push path identical to the normal push path** so you never bypass controls under pressure. Full checklist in cheatsheet.

### 6. Crisis Management — IMAG + OPSEC (Ch 17–18)
**IMAG** (Incident Management at Google, modeled on the US Incident Command System): assign an **Incident Commander** immediately, triage (false positive / playbook / crisis), parallelize investigate/contain/communicate streams, structured handovers. **OPSEC rule**: during a *security* incident, move to **out-of-band channels** — assume the adversary is watching your normal tools. Recovery uses a **separate team**, rebuilds from clean state, and **rotates all potentially exposed credentials**.

### 7. Culture (Ch 19–21)
Security is a **team responsibility, not a gatekeeping function**. Embed it early (architecture-time, not launch-time), keep postmortems **blameless**, and action red-team findings — or you find the same holes year after year. "Hope is not a strategy" applies to security too.

---

## Chapter Index

| # | Title | Key Framework |
|---|-------|---------------|
| [ch01](chapters/ch01-intersection-security-reliability.md) | The Intersection of Security and Reliability | CIA triad; fail-safe vs fail-secure |
| [ch02](chapters/ch02-understanding-adversaries.md) | Understanding Adversaries | Three-framework attacker model |
| [ch03](chapters/ch03-case-study-safe-proxies.md) | Case Study — Safe Proxies | Safe Proxy model |
| [ch04](chapters/ch04-design-tradeoffs.md) | Design Tradeoffs | Feature vs emergent requirements |
| [ch05](chapters/ch05-design-for-least-privilege.md) | Design for Least Privilege | Least privilege, MPA, Zero Trust |
| [ch06](chapters/ch06-design-for-understandability.md) | Design for Understandability | System invariants, type-safe APIs |
| [ch07](chapters/ch07-design-for-changing-landscape.md) | Design for a Changing Landscape | Change characteristics framework |
| [ch08](chapters/ch08-design-for-resilience.md) | Design for Resilience | Defense in depth, blast radius |
| [ch09](chapters/ch09-design-for-recovery.md) | Design for Recovery | Four failure categories |
| [ch10](chapters/ch10-mitigating-dos.md) | Mitigating Denial-of-Service Attacks | Attacker economics, layered defense |
| [ch11](chapters/ch11-case-study-trusted-ca.md) | Case Study — Publicly Trusted CA | Build vs buy framework |
| [ch12](chapters/ch12-writing-code.md) | Writing Code | Security/reliability via frameworks |
| [ch13](chapters/ch13-testing-code.md) | Testing Code | Security testing pyramid; fuzzing |
| [ch14](chapters/ch14-deploying-code.md) | Deploying Code | Supply-chain threat model, provenance |
| [ch15](chapters/ch15-investigating-systems.md) | Investigating Systems | Structured debugging; logging tradeoffs |
| [ch16](chapters/ch16-disaster-planning.md) | Disaster Planning | Disaster risk analysis; DiRT |
| [ch17](chapters/ch17-crisis-management.md) | Crisis Management | IMAG; OPSEC |
| [ch18](chapters/ch18-recovery-and-aftermath.md) | Recovery and Aftermath | Parallel recovery teams; credential rotation |
| [ch19](chapters/ch19-case-study-chrome-security.md) | Case Study — Chrome Security Team | Security as shared responsibility |
| [ch20](chapters/ch20-roles-and-responsibilities.md) | Understanding Roles and Responsibilities | Distributed security responsibility |
| [ch21](chapters/ch21-building-a-culture.md) | Building a Culture of Security and Reliability | Six dimensions of healthy culture |

## Topic Index

- **Adversaries / threat modeling** → ch02, ch14
- **Blast radius / compartmentalization** → ch08, ch01
- **Build provenance / supply chain** → ch14, ch12
- **CIA triad** → ch01
- **Crisis management / IMAG / OPSEC** → ch17, ch18
- **Culture / blameless postmortem** → ch19, ch20, ch21
- **Defense in depth** → ch08, ch01
- **Design tradeoffs (fail-safe vs fail-secure)** → ch01, ch04
- **DoS mitigation / layered rate limiting** → ch10
- **Least privilege / MPA / Zero Trust** → ch05, ch03
- **Recovery / rebuild from clean state / credential rotation** → ch09, ch18
- **Resilience / graceful degradation** → ch08
- **Safe Proxy** → ch03, ch05
- **System invariants / understandability / type-safe APIs** → ch06, ch12
- **Testing / fuzzing** → ch13
- **Zero Trust networking** → ch05

## Supporting Files

- [glossary.md](glossary.md) — key terms with definitions
- [patterns.md](patterns.md) — concrete patterns (Safe Proxy, RPC interceptor, type-enforced invariants, binary provenance, immutable rebuild, blameless postmortem)
- [cheatsheet.md](cheatsheet.md) — security×reliability decision table, least-privilege controls, supply-chain checklist, DoS layers, IMAG quick reference, recovery checklist

---

## Scope & Limits

Covers the book's content only. Google-specific tooling (BeyondCorp, BeyondProd, Borg) is referenced as concrete examples of the principles, not as required implementations — extract the principle, adapt the mechanism. For SLO/error-budget depth pair with `hidalgo-slo`; for operational SRE practice pair with `google-sre` / `sre-workbook`; for debugging telemetry pair with `observability-engineering`.
