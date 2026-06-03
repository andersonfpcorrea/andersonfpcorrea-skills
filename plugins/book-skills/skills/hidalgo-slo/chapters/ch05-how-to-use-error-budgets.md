# Chapter 5: How to Use Error Budgets

## Core Idea
Error budgets are a communication and decision-making framework, not a rigid rule. They exist to help you have better conversations about when to ship features vs. when to focus on reliability. Never freeze your release pipeline blindly — treat it like a budget.

## Frameworks Introduced

- **Error Budget Decision Model** (basic):
  - Budget remaining → ship new features, experiment, run chaos engineering
  - Budget depleted → focus on reliability, pause risky releases

- **Events-Based Error Budget Math**:
  ```
  error_budget_total = SLO_headroom × total_requests
  # Example: SLO = 99.8% → budget = 0.2% of requests
  remaining_pct = (budget_total_pct - actual_failure_pct) / budget_total_pct
  ```

- **Time-Based Error Budget Math**:
  ```
  total_data_points = 1 × resolution_factor × minutes × hours × days
  # e.g., 30-day, 1-second resolution: 1 × 60 × 60 × 24 × 30 = 2,592,000
  budget_points = (1 - SLO_target) × total_data_points
  remaining = budget_points - bad_observations
  ```

- **Rolling vs. Calendar-Bound Windows**:
  - **Rolling**: Bad events expire after the window. More operationally accurate.
  - **Calendar-bound**: Budget resets on a date. Easier for reporting; risk of release flood at reset.

- **Error Budget Policy**: A formalized document specifying what actions the team takes at various burn levels (e.g., "at 20% burn → review risk factors; at 100% burn → freeze risky releases and pivot to reliability").

## Key Concepts

- **Error budget surplus**: Budget remaining within the current window.
- **Error budget deficit**: Budget exceeded; now accumulating negative.
- **Error budget burn**: Rate at which budget is being consumed (e.g., "3 minutes/hour").
- **Error budget recovery**: Budget returning as old bad events age out of the rolling window.
- **Purposely burning budget**: Advanced technique — if a service consistently runs well above its SLO, deliberately exhaust remaining budget at quarter-end to surface hidden hard dependencies (the "Chubby story" from Google).
- **Budget for humans**: Apply error budget thinking to ticket queues, PR review latency, vacation usage, or retrospective cadences — anything that needs a "good enough" target.

## Time Window Selection

- **30 days** is the most common default — aligns with billing cycles, familiar to humans.
- **90 days or yearly** for major customer-facing products — users remember failures longer than 30 days.
- **Exclude maintenance windows**: Remove known planned downtime from budget calculations.
- **Do not use calendar windows for SLA-tied services** without understanding the month-boundary flood pattern.

## Example Calculations

**Events-based (surplus):**
- 20,000,000 total requests, 36,513 failures, SLO = 99.8%
- Budget = 0.2% → allowed = 40,000 failures
- Actual rate = 36,513/20,000,000 = 0.183%
- Remaining = 0.2% − 0.183% = 0.017% → 8.5% of budget left

**Time-based (full example):**
- 30-day window, 1-second resolution: 2,592,000 total seconds
- SLO = 99.7% → budget = 0.3% × 2,592,000 = 7,776 bad seconds (≈ 2h 9m 36s)
- 3,888 bad observations → 50% of budget consumed; 3,888 s (≈ 1h 4m 48s) remaining

## Error Budget Uses Beyond Feature Freeze

- **Risk factor analysis**: Track which events correlate with budget burn over time.
- **Chaos engineering and blackhole exercises**: Do them when budget is healthy.
- **Load and stress testing**: Safe when budget is ample; stop before exceeding budget.
- **Experimentation**: Swap libraries, change config, try new architectures on surplus budget.

## Anti-patterns

- **Freezing all releases when budget runs out**: This creates a release flood when the window resets, often causing the same problem. Think budget, not binary freeze.
- **Treating error budgets as a hard rule**: The data is a model; use judgment. Black swan events may require ignoring it temporarily.
- **Calendar-bound windows without accounting for reset flood**.
- **Not documenting the error budget policy**: Without a pre-agreed policy, teams won't act on the data.

## Key Takeaways

1. Error budgets are primarily a communication framework — they structure conversations about reliability vs. features.
2. Both events-based and time-based calculations are useful; use both.
3. Choose a time window humans can reason about — 30 days for most services, 90 days for customer-facing products.
4. Write a formal error budget policy before you need it.
5. Advanced: purposely burn remaining budget to expose hidden hard dependencies.

## Connects To

- **Ch 8**: Burn-rate alerting on error budgets
- **Ch 6**: Getting organizational buy-in to actually act on error budget policies
- **Ch 13/14**: Using error budget data to evolve culture and SLO targets
