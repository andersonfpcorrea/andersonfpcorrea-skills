# Chapter 10: Practical Alerting from Time-Series Data

## Core Idea
Borgmon (and its open-source successors like Prometheus) replaced threshold scripts with a time-series rule language — enabling alert computation over history and distributions, not just instantaneous values.

## Frameworks Introduced
- **Time-Series First Architecture**: Collect all metrics as time-series; compute alerts from rules over those series. Charts and alerts share the same data source.
- **Borgmon/varz pattern**:
  ```
  curl http://service:port/varz
  http_requests 37
  errors_total 12
  http_responses map:code 200:25 404:0 500:12
  ```
  Every binary exposes `/varz` (or equivalent) via HTTP. Borgmon scrapes on schedule, stores in-memory, evaluates rules.
- **Hierarchical Borgmon topology**: One Borgmon per cluster (scrapes tasks); one or two global Borgmon (aggregates clusters). Discard data strategically at each level to manage scale.

## Key Concepts
- **Single-machine alerts are unacceptable**: At Google scale, individual machine failures are too noisy. Alert on service-level objectives, not individual component failures.
- **Synthetic variables**: Borgmon creates synthetic metrics per target: DNS resolution success, collection success, health check result, collection time — provides monitoring of the monitoring.
- **Rule evaluation**: Borgmon evaluates aggregation rules to create derived time-series, then alerting rules fire when derived series cross thresholds. Rules can compute rates, ratios, percentiles.
- **Black-box monitoring complement**: Borgmon is white-box; a separate prober (black-box) tests externally visible behavior. Both needed — white-box for root-cause, black-box for symptom detection.
- **Alert management**: Borgmon sends to alertmanager, which handles deduplication, routing to on-call, inhibition, and silencing.
- **Schemaless exposition trade-off**: Low barrier to adding metrics (positive for agility) vs. change management complexity (decoupled from code — requires validation tooling).

## Mental Models
- "May the queries flow, and the pager stay silent." — The ideal is comprehensive monitoring with near-zero pages.
- Service-level aggregation: alert on "error rate for my service" not "server X is down." The former is user-visible; the latter is noise.
- Use rate-based rules (e.g., `rate(errors[5m])`) not raw counts — raw counts don't normalize for traffic fluctuations.
- Prometheus is Borgmon's open-source spiritual successor — all principles in this chapter apply directly.

## Anti-patterns
- **Polling scripts for alerting**: Short-lived processes can't use historical context; they check a single instant only.
- **One Borgmon per binary**: Creates N² connections; use the hierarchical topology instead.
- **Alerting on machine metrics directly**: "Being alerted for single-machine failures is unacceptable because such data is too noisy to be actionable."
- **No validation tooling for exported variables**: Schemaless exposition requires automated validation to catch changes that break alert rules.

## Reference Tables
| Borgmon Topology Level | Scope | Aggregation Action |
|---|---|---|
| Scraper Borgmon | Subset of tasks in cluster | Raw data collection |
| Cluster Borgmon | All tasks in one cluster | Aggregate by job/datacenter |
| Global Borgmon | All clusters worldwide | Global SLO view, discard per-task detail |

## Key Takeaways
1. Time-series–based alerting (Borgmon/Prometheus) supersedes check-script monitoring — history enables smarter rules (rates, trends, distributions).
2. Alert on service objectives (error rate, latency percentile) not machine health.
3. Every binary exposes `/varz` — zero-friction instrumentation lowers the barrier to good observability.
4. Hierarchical topology scales monitoring: scrape → cluster aggregate → global aggregate. Each level discards fine-grained data.
5. Combine white-box (internal metrics) with black-box (external probing) for complete coverage.

## Connects To
- **Ch 6**: Monitoring philosophy and Four Golden Signals — the "what to measure" complement to this chapter's "how to measure."
- **Ch 4**: SLOs — the targets that alerting rules are measured against.
- **Ch 11**: Being On-Call — the destination of alerts.
