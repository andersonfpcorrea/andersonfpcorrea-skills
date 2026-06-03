# Chapter 1: How SRE Relates to DevOps

## Core Idea
SRE is a concrete implementation of DevOps principles — "class SRE implements interface DevOps" — with additional prescriptions: ops is a software problem, SLO-driven decisions, toil reduction, and shared ownership with developers.

## Frameworks Introduced
- **SRE-as-DevOps implementation**: DevOps is an interface; SRE is one opinionated implementation. Both reject silos, embrace gradual change, and treat measurement as crucial — but SRE adds specific numerical targets (SLOs), an explicit toil budget (≤50%), and structural constraints.
- **Five SRE axioms**:
  1. Operations is a software problem
  2. Manage by SLOs
  3. Work to minimize toil
  4. Automate this year's job away
  5. Move fast by reducing the cost of failure

## Key Concepts
- **DevOps**: culture/practice removing dev/ops silos; accidents are normal; change should be gradual; tooling and culture interrelated
- **SRE**: software-engineering approach to operations; explicit SLOs, error budgets, toil cap
- **Cost of failure**: reliability work's value measured by its impact on SLO/error budget, not effort
- **Parity of esteem**: SRE career/financial parity with development roles — required for sustainable SRE adoption

## Mental Models
- Use "class SRE implements interface DevOps" to explain SRE to DevOps practitioners — they share goals; SRE adds prescriptions.
- Treat reliability as a feature; prioritize by error-budget impact, not intuition.
- Ask "when" (when is this worth doing?) rather than "whether" — most reliability decisions are about timing, not feasibility.

## Anti-patterns
- **Narrow, rigid incentives**: rewarding only uptime or only features creates the silo that DevOps and SRE both try to dissolve.
- **Blame someone else**: blameless culture is not optional — blame kills learning and drives the wrong behaviors.
- **SRE as ops cost center**: treating SRE as pure cost without recognizing the engineering leverage destroys its value.

## Key Takeaways
1. SRE and DevOps are complementary; SRE is one way to operationalize DevOps culture.
2. Without SLOs, there is no principled reason for SREs to exist — SLOs are the foundation.
3. Organizational incentives must align for SRE adoption to succeed; technical changes alone are insufficient.

## Connects To
- **Ch 2**: SLO implementation is the first concrete step from principles to practice
- **Ch 20**: SRE team lifecycle — organizational adoption patterns
