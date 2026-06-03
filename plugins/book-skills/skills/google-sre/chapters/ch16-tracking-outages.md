# Chapter 16: Tracking Outages

## Core Idea
Postmortems cover individual high-impact events; Outalator covers the aggregate pattern — tracking all alerts enables measuring reliability improvement over time and identifying systemic issues invisible to individual postmortems.

## Frameworks Introduced
- **Outalator**: Outage tracking system that passively receives all alerts, enables grouping into incidents, tagging with causes/actions, and aggregate analysis.
- **Alerts vs. Incidents distinction**: Multiple alerts → one incident. Separate analysis of "alerts per day" vs. "incidents per day" reveals signal-to-noise ratio.
- **Tagging taxonomy**: `cause:network`, `cause:network:switch`, `action:rollback`. Hierarchical namespace with colons enables filtering and trend analysis across teams.

## Key Concepts
- **Escalator**: Alert escalation system — if no acknowledgment after N minutes, escalate to secondary on-call → team lead → etc. Transparent; receives copies of alert emails.
- **Aggregate analysis use cases**:
  - Alerts per on-call shift (toil measurement)
  - Actionable vs. non-actionable alert ratio (alert quality)
  - Which service creates the most toil (prioritization)
  - Cross-team horizontal impact patterns (systemic issues)
- **Limitation of postmortems alone**: Written for high-impact incidents only; miss frequent-but-small issues that cumulatively drain engineering time.
- **Important annotations**: Mark critical context notes as "important" to surface them while collapsing noise. Key during a live incident to track timeline.
- **Meta-analysis**: Aggregate postmortem data enables machine learning to predict weaknesses and identify cross-team patterns.

## Mental Models
- "Improving reliability over time is only possible if you start from a known baseline and can track progress."
- Alert data is a time series just like application metrics — trend it, set SLOs on it, improve it.
- Individual postmortems are like bug reports; Outalator is like a bug tracker that aggregates them into patterns.

## Anti-patterns
- **Only postmortems for reliability tracking**: Misses the long tail of small, frequent issues that consume more cumulative time than rare large ones.
- **Untagged alerts**: Raw alert counts with no metadata make trend analysis impossible.
- **Alert count as reliability metric**: Raw alerts include noise/test events; incidents (grouped alerts) are the actual signal.

## Key Takeaways
1. Track all alerts (not just postmortems) to measure reliability baseline and improvement over time.
2. Group alerts into incidents for meaningful metrics; "incidents per day" is actionable, "alerts per day" is noisy.
3. Tag every incident with cause and action using a consistent hierarchy — enables horizontal pattern detection.
4. Escalation chain must be configured for all alerts — unacknowledged pages become higher-severity incidents.
5. Aggregate analysis across teams catches systemic issues that single-service postmortems cannot see.

## Connects To
- **Ch 15**: Postmortems — Outalator complements, not replaces, individual postmortems.
- **Ch 6**: Monitoring — Outalator is the downstream consumer of all monitoring alerts.
- **Ch 11**: Being On-Call — "alerts per shift" is the key toil metric from Outalator.
