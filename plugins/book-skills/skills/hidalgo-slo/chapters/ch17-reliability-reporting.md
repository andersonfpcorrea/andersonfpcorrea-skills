# Chapter 17: Reliability Reporting

## Core Idea
SLOs are the best data source for reliability reporting because they directly represent user experience. Traditional reporting methods (incident counts, severity levels, MTTX) are ambiguous, subjective, and misleading. Replace them with error budget status and SLO compliance trends.

## Why Traditional Reporting Fails

### Counting Incidents
- "Incident" is ambiguous: page triggered? customer complaint? status page update?
- Different teams apply inconsistent standards for what counts.
- Frequency doesn't capture severity or user impact.

### Severity Levels (S0–S5)
- Definitions are never precise enough to classify all real incidents unambiguously.
- Flapping between severity levels during a single incident creates classification problems.
- 15 seconds of S0 during a longer S3 incident distorts the classification.
- Time of day matters (S0 at 03:00 with zero traffic ≠ S0 at peak).

### MTTX (Mean Time to Recovery, Resolution, Detection, etc.)
- At least 15 variations of MTTX are in common use — no consensus on which matters.
- Arithmetic means are sensitive to outliers; a 3-day outage overwhelms 100 normal incidents.
- Doesn't capture user impact: a 5-minute P99 latency spike may matter more than a 1-second S1.
- Exception: MTTX for hardware failure rates is valid and useful (non-incident context).

## SLO-Based Reporting

### What to Report

**Error budget status:**
- "We have 30 minutes of error budget remaining this month." (Immediately actionable)
- "We have exhausted 2.3× our quarterly error budget." (Requires immediate escalation)

**SLO compliance trend:**
- "We were compliant with our 99.9% SLO for 11 of the last 12 months."
- "Our 30-day rolling SLO compliance has declined from 99.92% to 99.85% over the past quarter."

**Burn rate context:**
- "During the incident on March 3, we burned 18% of our monthly error budget in 2 hours."

### Reporting Audiences

| Audience | What they need | Format |
|---|---|---|
| Engineers | Error budget status, burn rate, SLO trend | Dashboard + alerts |
| Product | Feature-vs-reliability balance, error budget by change type | Weekly digest |
| Operations | Incident attribution to SLO burn | Post-incident report |
| Leadership | Aggregate SLO compliance, user impact summary | Monthly/quarterly review |
| External customers | Reliability commitment status | Public status page |

### The SLO Dashboard (Minimum Content)
- Current SLO compliance percentage for active window
- Error budget remaining (time and percentage)
- Burn rate over the last 1 hour, 24 hours, 7 days
- History of SLO compliance for the last N windows
- Trend: is reliability improving or degrading?

## Using SLO Data for Incident Lifecycle

- **During incident**: "We're burning budget at 5× the normal rate" provides actionable urgency.
- **Post-incident**: "This incident burned 12% of our monthly error budget" places severity in user-impact context.
- **Retrospective**: "This type of incident accounts for 60% of our annual error budget burn" directs investment.

## Anti-patterns

- **Using MTTX as the primary reliability metric for reporting to leadership**: It's too easily gamed and too ambiguous.
- **Severity levels without SLO context**: "We had 3 S1 incidents" is less informative than "we burned 40% of our monthly error budget in 3 events."
- **Reporting only to engineering**: Product and leadership need the data to align on investment.
- **No public status page**: External users have a right to understand planned reliability levels.

## Key Takeaways

1. Incident counts and MTTX are poor proxies for user-experienced reliability.
2. Error budget status and SLO compliance trend are the right primary metrics for reporting.
3. "We burned X% of our error budget" is clearer than "we had an S1 incident."
4. Tailor reporting format to audience: dashboards for engineers, trend summaries for leadership.
5. SLOs are self-reporting — the work of defining good SLIs pays dividends here.

## Connects To

- **Ch 5**: Error budget math that enables these reports
- **Ch 15**: Discoverable SLOs as the foundation for reporting
- **Ch 8**: Alert data that feeds into incident-level burn rate analysis
