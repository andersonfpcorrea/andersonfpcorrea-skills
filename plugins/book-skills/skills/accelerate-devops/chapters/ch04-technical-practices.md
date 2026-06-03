# Chapter 4: Technical Practices

## Core Idea
Continuous delivery — the set of technical practices enabling software to be deployable at any time — improves delivery performance, reduces burnout and deployment pain, improves culture, and increases quality.

## Frameworks Introduced
- **Five Principles of Continuous Delivery**:
  1. Build quality in (Deming's third point — stop depending on inspection).
  2. Work in small batches to get fast feedback and avoid zero-value work.
  3. Computers do repetitive tasks; people solve problems (automate regression testing, deployments).
  4. Relentlessly pursue continuous improvement — high performers are never satisfied.
  5. Everyone is responsible — system-level outcomes require cross-functional collaboration.

- **Three Foundations of Continuous Delivery**:
  1. Comprehensive configuration management — all production artifacts in version control; environments reproducible from code alone.
  2. Continuous Integration (CI) — short-lived branches (<1 day), frequent merges to trunk, fast automated tests on every commit.
  3. Continuous testing — automated tests run on every commit; developers own and can reproduce test failures locally.

## Key Concepts
- **Continuous delivery (CD)**: Software is in a deployable state throughout its lifecycle; fast feedback on deployability is available to all; the system can be deployed on demand at any time.
- **Deployment pipeline**: The automation that enforces CD — build, test, deploy in sequence; all changes flow through it.
- **Failure demand**: Work caused by not doing the right thing the first time; CD reduces it by building quality in.
- **Trunk-based development**: Fewer than three active branches; branch lifetimes < 1 day; no stabilization/code-freeze periods. Correlated with higher performance independent of team or org size.
- **Test automation ownership**: Tests primarily created and maintained by developers (not outsourced QA) — this makes code more testable and developers more invested in test quality.

## Anti-patterns
- **Long-lived feature branches**: Discourage refactoring, inhibit intra-team communication; lead to painful merge conflicts and delayed integration problems.
- **"Dev complete" before testing begins**: Testing is not a downstream phase — it must be continuous.
- **Outsourcing test creation to QA-only teams**: Not correlated with IT performance; developers must own automated tests.
- **Security as a downstream gate**: Late-stage security review slows delivery and creates large rework; shift left instead.

## Key Takeaways
1. Configuration in version control (system and application config, not just code) is more highly correlated with performance than code-only version control.
2. Trunk-based development outperforms long-lived branch strategies regardless of org size.
3. CD investments are investments in people: Microsoft Bing's work/life satisfaction jumped from 38% to 75% after implementing CD.
4. High performers spend 49% of time on new work, 21% on unplanned/rework; low performers spend 38% on new work, 27% on unplanned/rework.
5. CD predicts: higher delivery performance, lower change fail rate, generative culture, stronger org identity, lower burnout, less deployment pain.

## Connects To
- **Ch 3**: CD practices drive Westrum culture improvement.
- **Ch 5**: Loosely coupled architecture is the biggest single driver of CD capability (larger than test/deployment automation).
- **Ch 9**: CD reduces deployment pain and team burnout.
- **Appendix A**: CD capabilities 1–8 in the 24-capability list.
