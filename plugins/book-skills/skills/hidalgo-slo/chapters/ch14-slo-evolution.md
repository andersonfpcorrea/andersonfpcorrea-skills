# Chapter 14: SLO Evolution

## Core Idea
SLOs are not permanent fixtures. They must evolve as services change in usage, dependencies, features, and user expectations. Plan for change and schedule revisits — the conversations during SLO updates are some of the most valuable parts of the entire process.

## Reasons SLOs Change

### Usage Changes
- **Increased utilization**: May cause performance degradation (revisit target). May also allow tighter targets as statistical noise decreases with more data points.
- **Decreased utilization**: Fewer data points → wider target required. Nearing deprecation? May need no SLO at all.
- **Functional changes**: New features may change latency or error characteristics entirely.

### Dependency Changes
- A dependency's SLO tightens → your achievable SLO improves.
- A dependency degrades → your SLO may need to loosen.
- Dependencies are added or retired → compound reliability calculation changes.
- Platform changes (cloud provider, k8s version, database engine) can shift reliability up or down.

### Failure-Induced Changes
- Major incidents reveal that current targets were wrong (too tight or too loose relative to user tolerance).
- Use the incident as data: how did users respond? Did you lose any? What is the right target given this new information?

### User Expectation Changes
- Competitors launch more reliable services → users raise their expectations.
- Service matures → users accept it as critical infrastructure with higher expectations.
- Marketing or contracts create expectations your SLO doesn't support.

### Tooling Changes
- New measurement approaches may give you better or different data.
- Changing from time-series metrics to event logs may shift your calculated reliability.
- New SLO calculation tools may enable percentile-based SLOs where only availability-based ones existed before.

### Intuition-Based Changes
- Seasonal patterns (Black Friday, end-of-quarter): temporarily adjust error budget or SLO target.
- Significant reliability improvements from reliability work: target should become more stringent.
- Human intuition that the service "feels wrong" relative to the SLO: investigate and adjust.

## Aspirational SLOs

When current reliability is poor and users are unhappy, set a target better than current performance, then use the immediate error budget burn as a mandate for reliability work. As reliability improves, tighten the target incrementally.

## Identifying Incorrect SLOs

Signs an SLO is wrong:
- Users complain despite SLO being met (SLO is too loose — doesn't capture what users care about).
- SLO is never exceeded even during known incidents (SLO is far too loose).
- SLO is constantly burning without corresponding user complaints (SLO is too tight for current reality).
- Team cannot act on error budget data because the window is wrong.

## Revisit Schedules

Best practice: schedule SLO revisits proactively, not reactively.
- **New SLO**: Review after 1–2 months of data.
- **Stable SLO**: Quarterly or semi-annual review.
- **After major incidents**: Triggered review regardless of schedule.
- **After major feature launches**: Triggered review.

The revisit itself is valuable: it forces the team to have the reliability conversation even when things are going well.

## How to Change an SLO

1. Collect data supporting the change.
2. Document the rationale (user feedback, incident data, dependency changes).
3. Notify all downstream dependencies that rely on your SLO.
4. Update the SLO definition document (Ch 15).
5. Archive the old SLO definition with effective dates.
6. Update monitoring and alerting to reflect the new target.

## Key Takeaways

1. SLOs must evolve; plan revisit schedules proactively.
2. All seven change drivers (usage, dependencies, failures, user expectations, tooling, intuition, functional changes) should trigger SLO review.
3. Aspirational SLOs drive reliability work in a currently-broken service.
4. The conversations during SLO changes are as valuable as the new target itself.
5. Notify downstream dependencies when your SLO changes — they may have built on your old target.

## Connects To

- **Ch 4**: How to pick good targets (applies equally to revised targets)
- **Ch 13**: Cultural practices that support ongoing SLO iteration
- **Ch 15**: SLO documentation and version management
