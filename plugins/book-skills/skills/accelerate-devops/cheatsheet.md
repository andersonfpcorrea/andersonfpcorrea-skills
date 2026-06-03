# Cheatsheet — Accelerate: The Science of Lean Software and DevOps

## The Four DORA Key Metrics

| Metric | What It Measures | High Performer | Medium Performer | Low Performer |
|---|---|---|---|---|
| Deployment Frequency | Tempo / batch size | On demand (multiple/day) | Once/week – once/month | Once/month – once/6 months |
| Lead Time for Changes | Tempo / flow | < 1 hour | 1 week – 1 month | 1 month – 6 months |
| MTTR | Stability / resilience | < 1 hour | < 1 day | 1 day – 1 week |
| Change Failure Rate | Stability / quality | 0–15% | 0–15% | 31–45% |

**2017 gap: high vs. low:** 46× more deploys, 440× faster lead time, 170× faster MTTR, 5× lower change failure rate.

**Core thesis:** Speed and stability reinforce each other. High performers do better on all four metrics simultaneously.

---

## The 24 Capabilities to Drive Improvement

### Continuous Delivery (8)
1. Version control for all production artifacts
2. Deployment automation
3. Continuous integration
4. Trunk-based development
5. Test automation
6. Test data management
7. Shift left on security
8. Continuous delivery (CD)

### Architecture (2)
9. Loosely coupled architecture
10. Architect for empowered teams (tool choice)

### Product and Process (4)
11. Gather and implement customer feedback
12. Make flow of work visible through the value stream
13. Work in small batches
14. Foster and enable team experimentation

### Lean Management and Monitoring (5)
15. Lightweight change approval process (peer review, not CAB)
16. Monitor application and infrastructure to inform business decisions
17. Check system health proactively
18. WIP limits to improve processes and manage flow
19. Visualize work to monitor quality and communicate

### Cultural (5)
20. Support a generative culture (Westrum)
21. Encourage and support learning
22. Support and facilitate collaboration among teams
23. Provide resources and tools that make work meaningful
24. Support or embody transformational leadership

---

## Westrum Culture Quick Reference

| Pathological | Bureaucratic | Generative |
|---|---|---|
| Low cooperation | Modest cooperation | High cooperation |
| Messengers shot | Messengers neglected | Messengers trained |
| Responsibilities shirked | Narrow responsibilities | Risks shared |
| Bridging discouraged | Bridging tolerated | Bridging encouraged |
| Failure → scapegoating | Failure → justice | Failure → inquiry |
| Novelty crushed | Novelty → problems | Novelty implemented |

**How to move toward generative:** Change behaviors first (implement CD + Lean practices); culture follows.

---

## Five Dimensions of Transformational Leadership

1. **Vision** — Clear direction, five-year horizon
2. **Inspirational communication** — Motivates in uncertainty; makes people proud
3. **Intellectual stimulation** — Challenges assumptions; reframes problems
4. **Supportive leadership** — Cares for followers' personal needs
5. **Personal recognition** — Acknowledges achievement and quality improvements

---

## Anti-pattern Quick Reference

| Anti-pattern | Why It Fails |
|---|---|
| Change Advisory Board (CAB) | Negatively correlated with tempo AND stability; no better than no approval at all |
| Long-lived feature branches | Discourages refactoring; creates painful merges; delays integration feedback |
| Outsourcing strategic software | Low performers more likely to depend on custom software from another company |
| Maturity model thinking | Declares "done"; doesn't adapt to changing business/tech landscape |
| Utilization as productivity proxy | Queue theory: near 100% utilization → lead times approach infinity |
| Security as downstream gate | 2× remediation time vs. shift-left; creates bottlenecks before production |

---

## Key Research Numbers

- High performers: **2× as likely** to exceed organizational performance goals (profitability, productivity, market share)
- High performers: **50% higher** market capitalization growth over 3 years vs. low performers
- eNPS: high performers **2.2×** more likely to recommend their org as great place to work
- Security: high performers spend **50% less time** remediating security issues
- Burnout: stressful jobs as bad for health as **secondhand smoke**; costs US economy **$300B/year**
- New work vs. rework: high performers 49%/21%; low performers 38%/27%
