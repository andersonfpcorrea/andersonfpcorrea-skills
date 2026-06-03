# Google SRE Quick Reference

## Availability Table

| Nines | Annual Downtime | Quarterly Downtime |
|---|---|---|
| 99% (2 nines) | 3.65 days | 21.9 hours |
| 99.9% (3 nines) | 8.76 hours | 2.19 hours |
| 99.99% (4 nines) | 52.56 min | 13.14 min |
| 99.999% (5 nines) | 5.26 min | 1.31 min |

## Key Formulas

```
error_budget     = 1 − SLO_target
availability     = successful_requests / total_requests  (Google's preferred)
on-call floor    = 2 on-call weeks / N-person rotation  (≤ 25% target)
min rotation     = 8 SREs (single-site); 6/site (multi-site)
max incidents    = 2 per 12-hour on-call shift

adaptive throttle rejection prob:
  max(0, (requests − K × accepts) / (requests + 1))  [K typically = 2]

retry amplification: 3 layers × 3 retries each = 27× load at database layer
```

## Decision Tables

### When to Write a Postmortem
- User-visible downtime/degradation beyond threshold
- Data loss of any kind
- On-call engineer intervention (rollback, rerouting)
- Resolution time above threshold
- Monitoring failure (manual discovery)
- Any stakeholder request

### SLI Selection by Service Type
| Service | Key SLIs |
|---|---|
| User-facing | Availability, Latency (p99), Throughput |
| Storage | Latency, Availability, Durability |
| Big data | Throughput, End-to-end latency |
| All | Correctness |

### SRE Engagement Model Selection
| Situation | Engagement |
|---|---|
| Service needs high reliability + traffic | Full SRE engagement (PRR) |
| Service launching soon | LCE launch consultation |
| Team in ops mode | Embedded SRE (Ch 30) |
| Small service, not critical | Documentation + consultation |
| Design phase | Early engagement |

### Alert Quality Check (Before Creating)
1. Is it urgent, actionable, user-visible?
2. Will I ever ignore it? (If yes → redesign)
3. Does it require intelligence (not robotic response)?
4. Could the response be automated? (If yes → automate it)
5. Is someone else already getting this page?

## Critical Anti-patterns
- **Targeting 100% availability**: Impossible; costs exponentially more per additional nine
- **Averages for latency**: Use percentiles (p95/p99); averages hide tail behavior
- **QPS as capacity metric**: Use CPU consumption; QPS ignores request cost variance
- **Heartbeat-based leader election**: Cannot distinguish slow network from dead node → split-brain
- **Multi-layer retries without backoff**: 3 layers × 3 retries = 27× amplification
- **Email alerts**: Become alert spam; use dashboards for non-urgent signals
- **No playbooks**: Heroics don't scale; documented procedures improve MTTR ~3×
- **Freelancing during incidents**: Only Ops Lead touches production; others coordinate only
- **Replication as backup**: Replication propagates logical corruption; need independent backups
- **Commented-out dead code**: Time bomb; delete and rely on version control

## On-Call Rules of Thumb
- 5 min response for user-facing/revenue-critical; 30 min for less time-sensitive
- ≤ 2 incidents per 12-hour shift (each takes ~6 hours total work)
- ≤ 25% of SRE time on on-call (of the 50% non-engineering budget)
- Min rotation: 8 people (single-site), 6/site (multi-site, follow-the-sun)

## Toil Identification Checklist
Work is toil if it is: Manual + Repetitive + Automatable + Tactical + No enduring value + O(n) with service growth. Any 3+ of these = strong signal.

## Cascading Failure Escape Steps
1. Increase resources (if quick)
2. Stop health-check churn from redistributing load
3. Restart crash-looping servers
4. **Drop traffic** (counter-intuitive but essential)
5. Enter graceful degradation mode
6. Eliminate retry storms and batch jobs
