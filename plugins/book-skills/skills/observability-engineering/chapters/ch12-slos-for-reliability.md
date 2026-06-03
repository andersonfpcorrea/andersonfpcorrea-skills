# Chapter 12: Using Service-Level Objectives for Reliability

## Core Idea
SLO-based alerting decouples the "what" (user experience is degraded) from the "why" (which system component failed), replacing thousands of noisy threshold alerts with a small number of reliable, actionable signals. Observability is required to make SLO alerts debuggable.

## Frameworks Introduced
- **Two-criteria alert quality test**: An alert is helpful only if it (1) reliably indicates degraded user experience, AND (2) is actionable (there's a systematic way to investigate and fix it). Any alert failing either criterion should be deleted.
- **SLO structure**:
  - **SLI (Service Level Indicator)**: Measurement of whether an event is "good" or "bad" (e.g., request duration < 100ms = good).
  - **SLO (Service Level Objective)**: Internal reliability target (e.g., 99.9% of events must be good over a 30-day window).
  - **Error budget**: Maximum allowed "bad" events derived from the SLO. Spending error budget triggers investigation.
  - **SLA (Service Level Agreement)**: External commitment to customers; SLOs are typically more stringent.

## Key Concepts
- **Alert fatigue**: Caused by high false-positive rate from potential-cause alerts (CPU, memory, threads). Leads to normalization of deviance — engineers ignore alerts until catastrophic failure.
- **Normalization of deviance**: Term coined during the Challenger investigation. When deviations from expected response become "normal," critical failures are missed.
- **Potential-cause alerts**: Trigger on underlying system conditions (CPU > 80%) that may or may not indicate user impact. Prone to false positives.
- **Symptom-of-user-pain alerts**: Trigger when the user experience is actually degraded. Much higher signal-to-noise ratio.
- **Event-based SLI**: Each individual request classified as good or bad. Preferred over time-based.
- **Time-based SLI**: Each time interval (e.g., 5-minute window) classified as good or bad. Coarser, more false positives and negatives.
- **Decoupling "what" from "why"**: SLO alerts tell you *that* something is wrong; observability tells you *why*.

## Mental Models
- Delete all alerts that don't pass the two-criteria test: reliable indicator of user impact + actionable. What remains is a small set of high-quality alerts.
- SLOs without observability are incomplete: you know something is wrong but cannot debug it. Observability without SLOs has no clear alerting trigger.
- Think of the error budget as a production research budget: while budget remains, engineering can ship features; when budget is exhausted, shift to reliability work.

## Anti-patterns
- **Potential-cause alerting**: Alerting on CPU, memory, thread counts that may or may not correlate with user impact. The response requires divining the correlation.
- **Alert accumulation after incidents**: Post-incident reviews add alerts that create more noise in the next incident, compounding fatigue.
- **SLOs without observability**: Knowing user experience is degraded is meaningless if you cannot investigate why.

## Key Takeaways
1. Alert fatigue is a structural problem caused by threshold-based monitoring, not a discipline problem.
2. Good alerts: reliable indicator of user pain + actionable. Bad alerts: everything else. Delete everything else.
3. SLOs use event-based SLIs for higher granularity and fewer false positives/negatives than time-based metrics.
4. Decoupling "what" (SLO) from "why" (observability) is the key architectural insight.
5. The cultural shift to SLO-based alerting requires proof: run SLO alerts in parallel with traditional alerts until SLOs have demonstrated they catch real issues (as the Honeycomb case study shows).

## Connects To
- **Ch 13**: Detailed mechanics of error budget burn calculations and predictive burn alerts.
- **Ch 8**: The core analysis loop is how you respond to and debug SLO-based alerts.
- **Ch 9**: SLOs sit at the intersection of monitoring (infrastructure health) and observability (application health).
