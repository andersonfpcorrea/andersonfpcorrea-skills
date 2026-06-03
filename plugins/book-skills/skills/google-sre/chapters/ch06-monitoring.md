# Chapter 6: Monitoring Distributed Systems

## Core Idea
Monitoring should tell you *what* is broken (symptoms), not just *why* (causes). Pages must be actionable and represent novel problems. The Four Golden Signals cover every user-facing system.

## Frameworks Introduced
- **The Four Golden Signals**:
  1. **Latency**: Time to service a request. Track separately for successful and failed requests.
  2. **Traffic**: Demand on the system (HTTP req/s, transactions/s, network I/O rate — service-specific).
  3. **Errors**: Rate of failed requests (explicit 500s, implicit wrong content, policy violations like "SLO breaches").
  4. **Saturation**: How "full" the service is; the most constrained resource. Latency increases are leading indicators of saturation.
  > "If you can only measure four metrics of your user-facing system, focus on these four."

- **Symptoms vs. Causes**: Monitor for symptoms (what users see) for paging; investigate causes during debugging.
  - Page on: "HTTP 500s increasing" (symptom)
  - Diagnose: "Database connections failing" (cause)

- **Black-box vs. White-box**:
  - **Black-box**: Tests externally visible behavior; symptom-oriented; best for paging (already occurring, user-visible).
  - **White-box**: Inspects internals (logs, metrics endpoints); detects imminent problems, issues masked by retries; required for debugging.

## Key Concepts
- **Three valid monitoring outputs**: Alert (page immediately), Ticket (act within days), Log (no action, diagnostic only).
- **Alert quality checklist**: Before creating any alert, ask:
  - Does this detect an urgent, actionable, user-visible condition?
  - Can I ever ignore it? If yes, fix or eliminate it.
  - Does it definitely indicate user impact?
  - Is the action urgent, or could it wait until morning?
  - Could the action be automated?
- **Tail latency matters**: If 1% of requests at 100 req/s take 5s, those are invisible in averages but destroy p99. Collect histograms, not means.
- **Resolution granularity**: CPU load needs second-level sampling; disk fullness checking once per 1–2 minutes is enough for 99.9% SLO targets.
- **Simplicity over cleverness**: Avoid "magic" threshold-learning systems. Rules that page must be simple, predictable, and reliable.
- **Alert fatigue**: Too many non-actionable pages cause engineers to ignore alerts, including real ones. Every page distracts from improvement work.

## Mental Models
- "Every time the pager goes off, I should be able to react with a sense of urgency. I can only react urgently a few times a day before I become fatigued."
- "Every page should be actionable. Every page response should require intelligence." — robotic responses should be automated away.
- "Spend much more effort on catching symptoms than causes." Symptoms are fewer and more stable.
- Bigtable lesson: Temporarily dial back SLO + disable email alerts when over-alerting. Reduced noise enabled faster real fixes.
- Gmail lesson: Scriptable alert responses are a red flag — automate or fix the root cause.

## Anti-patterns
- **Alerting on causes, not symptoms**: Cascading cause-alerts flood the pager and delay finding the real user-visible issue.
- **Email alerts as an alert tier**: "Alert spam" — rarely read, never acted on. Use a dashboard instead.
- **Complex dependency hierarchies**: Alert rules like "if database is slow, alert for slow DB; otherwise alert for slow website" are fragile and rarely maintained.
- **Monitoring everything**: Signals not in any dashboard and not triggering any alert should be removed.
- **Short-term pain avoidance over long-term fix**: Taking a controlled SLO hit to fix root causes beats perpetual firefighting.

## Key Takeaways
1. Page on symptoms (user impact), not causes. The Four Golden Signals cover every user-facing service.
2. Black-box monitoring for paging (ongoing, user-visible problems); white-box for debugging.
3. Every alert must be actionable, urgent, and novel. Robotic/predictable responses should be automated.
4. Tail latency matters more than averages — use histograms and percentiles.
5. Simplicity: monitoring systems that page must be simple, predictable, and robust. Complex systems break when you need them most.

## Connects To
- **Ch 1**: Three valid monitoring outputs (alert/ticket/log) introduced.
- **Ch 4**: Four Golden Signals → the canonical SLIs for user-facing services.
- **Ch 10**: Borgmon — the implementation of time-series monitoring at Google.
- **Ch 11**: Being On-Call — the human impact of alert quality.
