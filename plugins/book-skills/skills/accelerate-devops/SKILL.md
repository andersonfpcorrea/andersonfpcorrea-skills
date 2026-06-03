---
name: accelerate-devops
description: "Knowledge base from \"Accelerate: The Science of Lean Software and DevOps\" by Nicole Forsgren, Jez Humble & Gene Kim. Use when applying the DORA four key metrics (deployment frequency, lead time for changes, MTTR, change failure rate), the 24 capabilities framework, Westrum organizational culture typology, or transformational leadership dimensions. Core lane: research-backed delivery performance — why speed and stability reinforce each other, how to measure and improve them, and the organizational practices (technical, management, cultural) that enable high-performing technology organizations."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, capability name, chapter number, or 'index']"
---

# Accelerate: The Science of Lean Software and DevOps
**Authors**: Nicole Forsgren, Jez Humble, Gene Kim  |  **Pages**: ~286  |  **Chapters**: 16 + appendices  |  **Generated**: 2026-06-03

## How to Use This Skill

- **Without arguments** — load the core frameworks below (four key metrics, 24 capabilities, Westrum culture)
- **With a topic** — ask about `deployment frequency`, `Westrum`, `burnout`, `CAB`, `trunk-based development`, etc.; I find the relevant chapter
- **With chapter** — ask for `ch03` or `ch07`; I load that chapter file
- **Browse** — ask "what chapters do you have?" to see the full index

For details beyond Core Frameworks below, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md` files in this skill.

---

## Core Frameworks & Mental Models

### 1. The Core Thesis — Speed and Stability Are Not a Tradeoff (Ch 1, 2)

The central, research-verified finding: **high performers do better on every metric simultaneously** — faster deployments AND fewer failures AND faster recovery. The speed-vs-stability tradeoff is a false dichotomy. Building quality in enables both.

2017 data, high vs. low performers:
- **46× more frequent** code deployments
- **440× faster** lead time from commit to deploy
- **170× faster** MTTR after downtime
- **5× lower** change failure rate

High performers are also **2× as likely to exceed** organizational performance goals (profitability, productivity, market share). Software delivery performance is a business performance predictor.

---

### 2. The Four DORA Key Metrics (Ch 2)

Measure tempo and stability at the system level (not local/individual):

| Metric | What It Measures | High | Medium | Low |
|---|---|---|---|---|
| **Deployment Frequency** | Tempo; proxy for batch size | On demand (multiple/day) | Once/week – once/month | Once/month – once/6 months |
| **Lead Time for Changes** | Tempo; commit to production | < 1 hour | 1 week – 1 month | 1 month – 6 months |
| **MTTR** | Stability; incident recovery | < 1 hour | < 1 day | 1 day – 1 week |
| **Change Failure Rate** | Stability; quality | 0–15% | 0–15% | 31–45% |

Use these four metrics to benchmark, set targets, and track progress. Avoid vanity metrics (lines of code, velocity, utilization) — they measure outputs, not outcomes.

---

### 3. Westrum Organizational Culture (Ch 3)

Culture is measurable via Likert survey items (1–7 scale). Three types:

| Dimension | Pathological | Bureaucratic | Generative |
|---|---|---|---|
| Cooperation | Low | Modest | High |
| Messengers | Shot | Neglected | Trained |
| Failure | → Scapegoating | → Justice | → Inquiry |
| Novelty | Crushed | → Problems | Implemented |

**Generative culture predicts** software delivery performance, organizational performance, and job satisfaction. **Key mechanism:** information flows freely, enabling better decisions and faster correction.

**How to change culture:** Don't try to change beliefs first. Change behaviors — implement CD and Lean management practices. Culture follows. "You can act your way to a better culture."

In 2016: 31% pathological, 48% bureaucratic, 21% generative.

---

### 4. The 24 Capabilities (Appendix A)

Five categories. Each is independently measurable and improvable.

**Continuous Delivery (8):** Version control for all artifacts, deployment automation, CI, trunk-based development, test automation, test data management, shift left on security, CD.

**Architecture (2):** Loosely coupled architecture, architect for empowered teams (tool choice).

**Product & Process (4):** Customer feedback, flow visibility, small batches, team experimentation authority.

**Lean Management & Monitoring (5):** Lightweight change approval (peer review, not CAB), monitoring → business decisions, proactive system health, WIP limits, work visualization.

**Cultural (5):** Generative culture (Westrum), learning culture, cross-team collaboration, meaningful work/tools, transformational leadership.

**Use capabilities, not maturity levels.** Maturity models declare "done" and prescribe lock-step progression. Capability models drive continuous improvement tailored to each team's context.

---

### 5. Technical Practices — Continuous Delivery (Ch 4, 5)

Five CD principles: build quality in, work in small batches, automate repetitive tasks, relentlessly improve, everyone is responsible.

Key findings:
- **Trunk-based development** outperforms long-lived branches regardless of team or org size.
- **Configuration in version control** (system config, app config, build scripts) is *more* correlated with performance than code-only version control.
- **Developers owning test automation** (not outsourced QA) predicts performance.
- **Loosely coupled architecture** is the single biggest driver of CD capability (2017) — larger than test or deployment automation.
- High performers spend 49% on new work, 21% on unplanned/rework; low performers 38% and 27%.

---

### 6. Change Advisory Boards — the Research Verdict (Ch 7)

CABs are **negatively correlated** with lead time, deployment frequency, and restore time. No correlation with change failure rate. They are "worse than having no change approval process at all."

**Replace CABs with:** peer review (pair programming or PR review) + deployment pipeline. This satisfies segregation-of-duties requirements in regulated industries while improving performance.

---

### 7. Five Dimensions of Transformational Leadership (Ch 11)

Leadership influences performance indirectly — by enabling the technical and Lean practices that produce outcomes. Leaders amplify; they cannot substitute for capable practice.

1. **Vision** — Clear direction and five-year horizon
2. **Inspirational communication** — Motivates in uncertainty
3. **Intellectual stimulation** — Challenges assumptions; reframes problems
4. **Supportive leadership** — Cares for followers' personal needs
5. **Personal recognition** — Acknowledges achievement and quality

Teams with leaders in the **bottom third** of these characteristics are **half as likely** to be high performers.

---

### 8. Burnout and Sustainable Work (Ch 9)

Top five burnout predictors (all organizational, not individual): pathological culture, deployment pain, ineffective leaders, low DevOps investment, poor organizational performance.

Investments in CD and Lean practices improve both delivery performance AND team wellbeing — they are the same investment. Microsoft Bing: work/life satisfaction jumped from 38% to 75% after implementing CD.

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-accelerate.md) | Accelerate | Capabilities vs. maturity model, 24 capabilities overview |
| [ch02](chapters/ch02-measuring-performance.md) | Measuring Performance | Four DORA metrics, performance tiers table, speed/stability thesis |
| [ch03](chapters/ch03-measuring-culture.md) | Measuring and Changing Culture | Westrum typology, behavior-first culture change |
| [ch04](chapters/ch04-technical-practices.md) | Technical Practices | Five CD principles, trunk-based dev, test automation, unplanned work data |
| [ch05](chapters/ch05-architecture.md) | Architecture | Loose coupling, testability/deployability, inverse Conway, scaling law |
| [ch06](chapters/ch06-infosec.md) | Integrating InfoSec | Shift left, DevSecOps, 50% less remediation time |
| [ch07](chapters/ch07-lean-management.md) | Lean Management | WIP limits, visual displays, CAB vs. peer review |
| [ch08](chapters/ch08-product-development.md) | Product Development | Four Lean product capabilities, virtuous cycle |
| [ch09](chapters/ch09-sustainable-work.md) | Making Work Sustainable | Deployment pain, Maslach burnout model, five burnout factors |
| [ch10](chapters/ch10-employee-satisfaction.md) | Employee Satisfaction | eNPS, organizational identity, diversity data |
| [ch11](chapters/ch11-leaders-managers.md) | Leaders and Managers | Transformational leadership five dimensions, investment practices |
| [ch12-15](chapters/ch12-15-research-science.md) | The Research (Science) | Inferential predictive analysis, latent constructs, survey methodology |
| [ch16](chapters/ch16-transformation-ing.md) | ING Case Study | Obeya, tribe/squad/chapter, catchball, learning organization |

---

## Topic Index

- **Architecture (loosely coupled)** → ch05, ch04
- **Burnout** → ch09, ch03, ch11
- **CAB (Change Advisory Board)** → ch07, ch02
- **Capabilities (24 total)** → ch01, Appendix A (see cheatsheet.md)
- **Change failure rate** → ch02, cheatsheet.md
- **Continuous delivery** → ch04, ch05, ch08
- **Continuous integration** → ch04
- **Conway's Law / Inverse Conway** → ch05
- **Culture (Westrum)** → ch03, ch13
- **Deployment frequency** → ch02, cheatsheet.md
- **Deployment pain** → ch09, ch04
- **Diversity** → ch10
- **DORA metrics (four key metrics)** → ch02, cheatsheet.md
- **eNPS (employee NPS)** → ch10, ch11
- **Identity (organizational)** → ch10
- **ING Netherlands case study** → ch16
- **Inferential predictive analysis** → ch12-15
- **Lead time for changes** → ch02, cheatsheet.md
- **Lean management** → ch07
- **Lean product development** → ch08
- **Maturity model (anti-pattern)** → ch01
- **MTTR (mean time to restore)** → ch02, cheatsheet.md
- **Obeya** → ch16
- **Peer review (change approval)** → ch07
- **Psychometrics / latent constructs** → ch13 (within ch12-15 file)
- **Security (shift left)** → ch06, ch04
- **Surveys (why use them)** → ch12-15
- **Test automation** → ch04
- **Transformational leadership** → ch11, ch16
- **Trunk-based development** → ch04
- **Version control** → ch04
- **Virtuous cycle** → ch08
- **Westrum culture typology** → ch03
- **WIP limits** → ch07

## Supporting Files

- [glossary.md](glossary.md) — ~35 key terms with definitions
- [patterns.md](patterns.md) — 8 named patterns with When/How/Trade-offs
- [cheatsheet.md](cheatsheet.md) — four key metrics table, 24 capabilities list, Westrum table, anti-patterns, key research numbers

---

## Scope & Limits

This skill covers the book content (2018 edition). It does not include:
- DORA State of DevOps Reports published after 2018
- DORA Elite performer tier (added in later reports — the book uses high/medium/low)
- Successor research that extends or revises any findings
- Implementation details of specific tools (CI servers, deployment platforms, etc.)

For current DORA benchmarks, consult the annual State of DevOps Report. For implementing the practices, combine with project-specific engineering skills.
