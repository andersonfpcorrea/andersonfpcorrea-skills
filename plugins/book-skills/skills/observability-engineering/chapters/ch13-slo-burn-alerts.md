# Chapter 13: Acting on and Debugging SLO-Based Alerts

## Core Idea
Error budget burn rate alerts provide early warning before SLO exhaustion. Using event-based observability data for SLI calculation gives request-level granularity that time-series metrics cannot match — especially critical for high-availability targets (>99.95%).

## Frameworks Introduced
- **Sliding window (preferred) vs. fixed window**: Use a 30-day sliding window to track error budget burn. Fixed (calendar) windows reset abruptly and don't reflect human memory of outages. Shorter windows (<14d) don't align with product cycles; longer (>90d) mask incidents for too long.
- **Burn alert types**:
  - **Zero-level alert**: Triggers when error budget is fully exhausted. Baseline, always configure.
  - **Threshold alert**: Triggers when remaining budget dips below X% (e.g., 30%). Simple but crude — effectively moves the goalpost.
  - **Predictive burn alert** (preferred): Forecasts when budget will be exhausted based on current burn rate. Provides actionable advance warning.
- **Lookahead:baseline ratio rule**: "A given baseline window can linearly predict forward by a factor of four at most without needing to add compensation for seasonality." Practical heuristic: 1h baseline → 4h lookahead; 6h baseline → 24h lookahead.

## Key Concepts
- **Error budget**: Max allowed "bad" events. For 99.9% SLO over 30 days: ~43,800 typical requests → 438 allowed failures.
- **Short-term burn alert (ahistorical)**: Uses only the baseline window, assumes no prior errors. Computationally cheap but misses context about remaining budget.
- **Context-aware burn alert (historical)**: Tracks cumulative good/bad events across the entire SLO window. More sensitive when budget is nearly depleted. Computationally expensive (AWS Lambda cost discovery at Honeycomb: >$5,000/day with naive implementation).
- **Proportional extrapolation**: Project future burn proportionally to typical traffic volume (not linearly to raw failure count) — more accurate during low/high traffic periods.
- **Burn pattern types**: Gradual steady burn (background noise), burst pattern, single large incident. Different patterns suggest different urgency and investigation approach.

## Code Examples
```go
// Context-aware burn alert calculation (Go pseudocode)
func isBurnViolation(now time.Time, ba *BurnAlertConfig, slo *SLO, tm *timeseriesMap) bool {
    pOffset := time.Duration(ba.ExhaustionMinutes/lookbackRatio) * time.Minute
    pWindow := now.Add(-pOffset)
    tWindow := now.AddDate(0, 0, -slo.TimePeriodDays).Add(
        time.Duration(ba.ExhaustionMinutes) * time.Minute)

    var runningTotal, runningFails int64
    var projectedTotal, projectedFails int64
    for i := len(tm.Timestamps) - 1; i >= 0; i-- {
        t := tm.Timestamps[i]
        if t.Before(tWindow) { break }
        runningTotal += tm.Total[t]
        runningFails += tm.Fails[t]
        if t.After(pWindow) {
            projectedTotal += lookbackRatio * tm.Total[t]
            projectedFails += lookbackRatio * tm.Fails[t]
        }
    }
    projectedTotal += runningTotal
    projectedFails += runningFails
    allowedFails := projectedTotal * int64(slo.BudgetPPM) / int64(1e6)
    return projectedFails != 0 && projectedFails >= allowedFails
}
```

## Anti-patterns
- **Fixed window SLOs**: Customers don't reset their memory of reliability on the 1st of the month.
- **Time-series data for SLOs**: A 5-minute window is either good or bad; 94% of requests failing means that 5 minutes is entirely "bad" — burning 100% of the minute's budget vs. 6% with event-based calculation.
- **Single lookahead window**: Configure both short (1h→4h) and long (24h→96h) burn alerts — they catch different types of problems.

## Key Takeaways
1. Event-based SLIs: each request is good or bad. Time-based SLIs: each time window is good or bad. Event-based is always more granular and accurate.
2. For 99.99%+ SLOs, using time-series data can exhaust monthly budget in one 5-minute window. Event-based calculation buys significantly more response time.
3. Configure multiple burn alerts at different lookahead scales — short-term catches sudden failures; long-term catches gradual degradation.
4. The lookahead:baseline ratio of 4:1 is the practical safe limit for extrapolation without seasonality compensation.
5. Burning budget "all at once" vs. "slowly" suggests categorically different failure types and urgencies.

## Connects To
- **Ch 12**: Introduces SLOs; this chapter provides the mathematical implementation.
- **Ch 8**: Core analysis loop is how you investigate after a burn alert fires.
- **Ch 16**: Why event-based data stores (not TSDBs) are required for accurate SLI calculation.
