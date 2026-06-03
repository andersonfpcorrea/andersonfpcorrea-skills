# Cheatsheet — Building Secure and Reliable Systems

---

## Security × Reliability Decision Table

| Situation | Reliability Default | Security Consideration |
|---|---|---|
| System failure mode | Fail safe (open) | Fail secure (closed) |
| Incident information sharing | Broad — involve everyone | Need-to-know — adversary may be watching |
| Adding redundancy | Do it — improves availability | Every redundancy path widens attack surface |
| Rollback decision | Restore stability immediately | Does the rollback version have security vulnerabilities? |
| Logging strategy | More is better | Logs must not contain credentials or PII; ship to hardened system |
| Access during investigation | Grant broad read access | Even read access is a risk — control with least privilege + audit |

---

## Design Principles Quick Reference

| Principle | What It Means | Key Mechanism |
|---|---|---|
| Least Privilege | Minimum access for the task | Small functional APIs, MPA, Zero Trust |
| Defense in Depth | Multiple independent layers | Each layer assumes prior layer failed |
| Understandability | Can reason about invariants | Centralized auth, type-safe APIs, simple design |
| Design for Change | Frequent, reversible changes | Containers, microservices, feature flags |
| Design for Resilience | Withstand and delay breakage | Blast radius control, controlled degradation |
| Design for Recovery | Return to known good state | Fast rollout = fast recovery, explicit state |

---

## Least Privilege Controls — When to Apply What

| Risk Level | Controls |
|---|---|
| Low (public data, read) | Standard authentication |
| Medium (sensitive data, write) | Role-based access + audit logging |
| High (admin operations, infrastructure) | MPA required + business justification + temporary access |
| Critical (cryptographic keys, production access) | 3FA + MPA + HSM + breakglass-only |

---

## Supply Chain Security Checklist

- [ ] Code review is mandatory (no self-merge)
- [ ] All builds from VCS only (no local builds to production)
- [ ] CI generates and signs build provenance
- [ ] Deployment system verifies provenance before deploying
- [ ] Configuration changes go through the same pipeline as code
- [ ] Deployment choke point blocks direct production access
- [ ] Post-deployment validation confirms artifact matches provenance
- [ ] Deployment breakglass exists and is rarely used

---

## DoS Defense Layers (Outermost to Innermost)

1. **Edge routers** — ACLs dropping suspicious traffic; protect backbone bandwidth
2. **Network load balancers** — Rate limit packet floods; protect application LBs
3. **Application load balancers** — Rate limit by source IP/session; protect service frontends
4. **Service frontends** — Per-user/per-session rate limits; graceful degradation
5. **Service backends** — Protected by frontend rate limits; separate capacity pool

*Drop traffic as early (cheapest) as possible. Inner layers are capacity-planned only for traffic that breaches outer layers.*

---

## Incident Response Quick Reference (IMAG)

| Phase | Key Action |
|---|---|
| Alert received | Don't panic. Assign IC immediately. |
| Triage | False positive? Playbook event? Crisis? |
| Declaration | Formally declare; establish OPSEC channel |
| Response | Assign roles; parallelize streams (investigate / contain / communicate) |
| Handover | Structured context transfer — incoming IC confirms understanding |
| Recovery | Separate team; rebuild from clean state |
| Closure | Blameless postmortem; action items to completion |

**OPSEC rule**: During a security incident, communicate on out-of-band channels. Assume the attacker may be watching your normal communication tools.

---

## Recovery Checklist (Security Incident)

1. Scope: identify all potentially compromised systems + compromise timeline
2. Quarantine: network-isolate before rebuild
3. Rebuild: from known-clean images (not patch-in-place)
4. Data: validate backup timestamp predates compromise; sanitize before restore
5. Credentials: rotate ALL potentially exposed credentials (passwords, keys, certs, service accounts)
6. Validate: confirm rebuilt systems match expected state
7. Postmortem: root cause analysis; systemic improvements

---

## Culture Anti-patterns (What to Avoid)

| Anti-pattern | Why It Fails |
|---|---|
| Security team as gatekeeper | Teams route around; security seen as obstacle |
| Security review only at launch | Too late to fix architecture; expensive retrofits |
| Blame-driven postmortems | Incidents are hidden; systemic issues never fixed |
| All-or-nothing security | Over-applies controls to low-risk operations; friction causes bypass |
| Red team findings not actioned | Finds the same vulnerabilities year after year |
| Hope as a strategy | "Our engineers never make mistakes" is not a control |
