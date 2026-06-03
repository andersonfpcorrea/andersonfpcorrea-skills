# Chapter 4: Choosing Good Service Level Objectives

## Core Idea
A good SLO target sits exactly between "users are happy" and "users are unhappy." Don't chase nines — use data from past performance, basic statistics, and the context of dependencies and metric attributes to find the right number.

## Frameworks Introduced

- **Good SLO has two properties**:
  1. When exceeding the target, users are happy.
  2. When missing the target, users are unhappy.

- **The Problem of Being Too Reliable**: Being routinely more reliable than your SLO trains users to expect that higher level, creates operational underload (engineers don't learn from failures), and eliminates budget for chaos engineering and experimentation.

- **Dependency Math**: Compound unreliability. If 40 components each promise 99.9% reliability, the composed service reliability is `0.999^40 = 96.077%`.

- **Hard vs. Soft Dependencies**:
  - **Hard dependency**: Must be reliable for your service to function.
  - **Soft dependency**: Degradation impacts quality but service can still operate.
  - Converting hard deps to soft deps (e.g., via caching) is one of the best reliability investments.

- **Choosing Targets from Data** — the Five Ms:
  - **Min/Max**: Full scope of observed values.
  - **Mean**: Average; useful but can hide outliers.
  - **Median**: Middle value (50th percentile).
  - **Mode**: Most frequent value (less useful than percentiles for SLOs).

## Key Concepts

- **The Number Nine problem**: Targets like 99.9%, 99.99% are arbitrary. 99.7%, 98.62%, 97.2% are equally valid if data-driven.
- **Percentiles**: PX = the value below which X% of observations fall. P95, P99, P99.9 are commonly used for latency SLOs.
- **Long tail**: Latency distributions often have a narrow range at P95 and then a large range above it. Model both the "good" range and the tail.
- **Multiple SLOs for one metric**: Set P95 < 2,000 ms at 99.9%, P98 < 2,500 ms at 99.9%, P99 < 4,000 ms at 99.9% to cover the distribution.

## Reference Tables

**SLO targets in time per window:**

| Target | Per day | Per month | Per year |
|---|---|---|---|
| 99.999% | 0.9 s | 26.3 s | 5 m 15.6 s |
| 99.99% | 8.6 s | 4 m 23 s | 52 m 35.7 s |
| 99.9% | 1 m 26.4 s | 43 m 49.7 s | 8 h 45 m 57 s |
| 99.7% | 4 m 19.2 s | 30 m 14.4 s | 1 d 2 h 17 m |
| 99% | 14 m 24 s | 7 h 18 m 17.5 s | 3 d 15 h 39 m |

**Bad time formula:**
```
bad_seconds_per_day = (1 - SLO_target) × 86400
```

**Compound dependency reliability:**
```
composed_reliability = dependency_reliability ^ number_of_dependencies
```

## Metric Attributes Affecting Target Selection

- **Resolution**: If metrics arrive every 60 seconds and SLO = 99.95%, one bad data point = exceeded budget. May need to loosen target or require sustained violations.
- **Quantity**: Low-QPS services (e.g., 1 request/minute) have high variance. Single failures skew percentages dramatically. Use wider time windows.
- **Quality**: Noisy metrics may require requiring sustained violation (e.g., >50% of 5-minute window) before counting as bad.

## Anti-patterns

- **Only considering "nines"**: 99.7% is as legitimate as 99.9%. Pick based on data.
- **Not accounting for dependencies**: You can't be more reliable than your hard dependencies.
- **Too many SLOs**: Multiple comparison problem — with many SLOs you'll always find something "off," waste time on false signals, and confuse stakeholders.
- **Not changing targets over time**: User expectations shift; SLOs must evolve (see Ch 14).
- **Ignoring operational underload**: If you never fail, engineers don't learn; if SLOs are too tight, on-call engineers need sub-second response times.

## Key Takeaways

1. A good SLO target correlates with user happiness — exceeding it means freedom to ship, missing it means reliability work.
2. Don't constrain targets to "nines" — 98.62% is as valid as 99.9% if data-supported.
3. You cannot be more reliable than your hard dependencies; do the compound math.
4. Avoid too many SLOs; 3–5 per service is usually sufficient.
5. Without historical data, make an educated guess and iterate quickly.

## Connects To

- **Ch 5**: Using error budgets derived from these targets
- **Ch 9**: Advanced statistics (Bayesian, Poisson, distributions) for SLO math
- **Ch 14**: When and how to evolve SLO targets
