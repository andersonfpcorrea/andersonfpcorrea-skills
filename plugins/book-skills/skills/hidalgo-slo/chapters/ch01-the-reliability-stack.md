# Chapter 1: The Reliability Stack

## Core Idea
The Reliability Stack (SLI → SLO → Error Budget) is the foundational framework for thinking about service reliability from the user's perspective — not the operator's.

## Frameworks Introduced

- **The Reliability Stack**: Three-layer framework for operationalizing reliability
  - **SLI** (Service Level Indicator): A measurement of service behavior from the user's perspective, reducible to a ratio of good events to total events.
  - **SLO** (Service Level Objective): The target percentage for that ratio. "This service should succeed 99.9% of the time."
  - **Error Budget**: How much unreliability is permitted within a time window, derived from the SLO. If SLO = 99.9%, error budget = 0.1% of all events.

- **The Three Service Truths**:
  1. Reliability is the most important operational requirement of a service.
  2. Users determine reliability — what they perceive, not what your logs say.
  3. Nothing is perfect, and nothing has to be. 100% is impossible and undesirable.

## Key Concepts

- **SLI**: A metric that yields a binary "good" or "bad" outcome per event; the ratio of good events to total events forms the SLI percentage. Example: `59,982 / 60,000 = 99.97%`.
- **SLO**: A target percentage that should correlate with user happiness. Not a contract — can change.
- **SLA**: A contractual agreement with financial consequences, powered by similar SLIs. Hidalgo explicitly scopes this book to SLOs, not SLAs.
- **Error Budget**: `(1 − SLO_target)` × time_window = permitted unreliability. Can be expressed as bad events or bad minutes.
- **Events-based error budget**: Count bad events vs total; good for high-cardinality services.
- **Time-based error budget**: Count bad time intervals; better for human communication ("43 minutes remaining").
- **User**: Anything or anyone that relies on a service — humans, other services, robots.

## Mental Models

- Think of an SLI as "Is my service doing what users need?" reduced to a percentage.
- Error budgets are primarily a communication and decision-making framework — they exist to have better conversations, not to enforce rigid rules.
- SLOs are objectives, not contracts. Violating an SLO generates data; violating an SLA costs money.
- The SLO should sit between "users are happy" and "users are unhappy" — exceeding it means freedom to innovate; burning through it means focusing on reliability.

## Anti-patterns

- **Treating SLOs as a project**: SLOs are a process of continuous thinking, not a ticket you close.
- **Conflating uptime with reliability**: A service can be "up" but unreliable (returning wrong data, too slow). Uptime ≠ availability ≠ reliability.
- **Targeting 100%**: Impossible, increasingly expensive, and removes learning opportunities.
- **SLOs as buzzwords**: Using the term without the underlying thinking about users defeats the purpose.

## Service Types Covered

Web services, request/response APIs, data processing pipelines, batch jobs, databases/storage systems, compute platforms, hardware/network — all can have SLIs and SLOs.

## Key Takeaways

1. SLIs are the foundation — bad SLIs make everything else useless.
2. SLOs are the target — set to keep users happy, not to impress anyone.
3. Error budgets are the signal — they tell you when to ship vs. when to stabilize.
4. The entire framework is just a way to generate better data for better decisions.
5. It's a journey, not a destination; iterate on everything continuously.

## Connects To

- **Ch 3**: Developing meaningful SLIs (the foundation of the stack)
- **Ch 4**: Choosing good SLO targets
- **Ch 5**: Using error budgets in practice
- **Ch 8**: Alerting based on error budget burn rate
