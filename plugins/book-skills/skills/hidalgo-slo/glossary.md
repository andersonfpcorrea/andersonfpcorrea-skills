# Glossary — Implementing Service Level Objectives

**Accuracy** — The degree to which data correctly describes the entity, property, or event it represents. One of 7 data properties. (Ch 11)

**Alert fatigue** — The gradual decay of human response quality due to excessive, often false-positive alerts. A primary argument against threshold-based alerting. (Ch 8)

**Aspirational SLO** — An SLO target set better than current performance, used to drive reliability work when users are unhappy. (Ch 14)

**Availability** — Whether a service can respond to requests from users. Distinct from uptime (binary running state) and reliability (doing what it should). (Ch 3)

**Bayesian inference** — A statistical method incorporating prior beliefs with observed data to estimate unknown quantities. Useful for SLI calculations on sparse data. (Ch 9)

**Bernoulli trial** — An experiment with exactly two outcomes (success/failure), with fixed probability. Each request to a service is a Bernoulli trial. (Ch 9)

**Binomial distribution** — The probability distribution over the number of successes in n independent Bernoulli trials. Used to model expected SLI outcomes. (Ch 9)

**Blackhole exercise** — A chaos engineering technique of intentionally taking down an entire location (data center or region) to discover failure modes. (Ch 5)

**Brownfield** — An environment with existing monitoring, alerts, and processes. Contrasted with greenfield (clean slate). Requires transition strategy for SLO alerting. (Ch 8)

**Calendar-bound window** — An error budget window that resets on a calendar date (e.g., first of the month) rather than rolling continuously. (Ch 5)

**Completeness** — The degree to which a dataset includes all data items representing an entity or event. One of 7 data properties. (Ch 11)

**Compound reliability** — The reliability of a composed system: `r^N` where r is component reliability and N is number of components in series. (Ch 4, 9)

**Consistency** — How often all stores agree on a particular datum. One of 7 data properties. (Ch 11)

**Data lineage** — Tracking the provenance of data through processing stages; enables debugging of accuracy and freshness failures. (Ch 11)

**Durability** — The likelihood that a known-healthy copy of data exists. Failures are permanent and cannot be reversed. One of 7 data properties. (Ch 11)

**Error budget** — The amount of unreliability permitted within a time window, derived from: `(1 − SLO_target) × total_events`. (Ch 1, 5)

**Error budget burn** — Any consumption of error budget; often expressed as a rate (e.g., "3 minutes/hour"). (Ch 5)

**Error budget deficit** — When the error budget has been exceeded; calculations go negative. (Ch 5)

**Error budget policy** — A formalized document specifying what actions a team takes at various levels of error budget consumption. (Ch 5)

**Error budget recovery** — Error budget returning as old bad events age out of a rolling window. (Ch 5)

**Error budget surplus** — Budget remaining within the current window. (Ch 5)

**Events-based error budget** — Error budget calculated as a count of bad events vs. total events. Simpler math; works well with high-cardinality services. (Ch 5)

**Expected value** — The probability-weighted average outcome of a random process; denoted E[X]. (Ch 9)

**Exponential distribution** — Models time between events in a Poisson process. Memoryless property: knowing how long you've waited gives no information about future wait. (Ch 9)

**Fast burn** — An error budget burn pattern where budget depletes quickly (near-complete outage); requires short-window, high-sensitivity alerting. (Ch 8)

**Fog of war** — During large outages, spurious threshold alerts overwhelm responders' attention, hiding the actual cause. (Ch 8)

**Freshness** — How out-of-date data is relative to the most recent source input. Not the same as data age. One of 7 data properties. (Ch 11)

**Geometric distribution** — Models the number of trials until the first failure; P(failure on k-th trial) = (1−p)^(k−1) × p. (Ch 9)

**Hard dependency** — A dependency that must be reliable for your service to function. Your reliability ≤ hard dependency's reliability. (Ch 4)

**Histogram** — A bar chart where bars represent ranges or categories and heights represent counts or probabilities. Key tool for visualizing latency distributions. (Ch 4, 9)

**Hyrum's Law** — "With a sufficient number of users of an API, all observable behaviors will be depended on by somebody." Your reliability surface is larger than your SLA. (Ch 2)

**Implied agreement** — The level of reliability users have experienced and now expect to continue, even without a formal contract. (Ch 2)

**Integrity** — How trustworthy data is based on its governance and traceability. One of 7 data properties. (Ch 11)

**Latency SLO** — An SLO expressed in terms of response time. Often stated at percentiles: "P95 < 400 ms, 99.9% of the time." (Ch 3, 4)

**Long tail** — A distribution where most values cluster at the low end but a small percentage of values are very large. Common in latency distributions. (Ch 4)

**MLE (Maximum Likelihood Estimation)** — Statistical method that picks the parameters that make observed data most probable. Can overfit sparse data. (Ch 9)

**MTTX** — Mean time to <something>: recovery, detection, resolution, etc. Ambiguous, inconsistent, and poor proxy for reliability reporting. (Ch 17)

**Multiple comparison problem** — With many SLOs, some will appear "off" by chance alone, wasting time on false investigation. Argument for limiting SLO count. (Ch 4)

**Operational underload** — When a service is so reliable that operators rarely handle failures and lose the skill to do so. (Ch 4)

**P50, P95, P99, P99.9** — Percentile notation. P95 = the value below which 95% of observations fall. Standard notation for latency analysis. (Ch 4)

**Percentile** — A measure indicating the value below which a given percentage of observations fall. Core tool for SLO target selection. (Ch 4)

**Poisson distribution** — Models the number of rare events occurring at constant rate λ in a time period. Used for failure rate modeling and durability calculations. (Ch 9)

**PMF (Probability Mass Function)** — A function that gives the probability of each value in a discrete sample space. (Ch 9)

**Reliability** — Whether a service performs the duties it was designed to do. Distinct from uptime and availability. (Ch 2, 3)

**Reliability Stack** — Hidalgo's term for the three-layer SLI → SLO → Error Budget framework. (Ch 1)

**Rolling window** — An error budget window that continuously advances; old bad events expire after the window length. Recommended default. (Ch 5)

**Sample space** — All possible outcomes of a random experiment. For a request: {success, failure}. (Ch 9)

**Severity level** — A bucketed classification of incidents (S0–S5). Ambiguous and poorly suited for reliability reporting compared to error budget status. (Ch 17)

**SLA (Service Level Agreement)** — A contractual commitment to users that includes financial consequences for violations. Distinct from SLO. (Ch 1)

**SLI (Service Level Indicator)** — A measurement of service behavior from the user's perspective, expressed as a ratio of good events to total events. (Ch 1, 3)

**Slow burn** — An error budget burn pattern where budget depletes gradually over days; requires longer-window, lower-sensitivity alerting. (Ch 8)

**SLO (Service Level Objective)** — A target percentage for an SLI ratio. The goal that correlates with user happiness. Not a contract. (Ch 1, 4)

**Soft dependency** — A dependency whose failure degrades but does not eliminate service functionality. (Ch 4)

**Time-based error budget** — Error budget calculated as bad time intervals rather than bad event counts. Better for human communication. (Ch 5)

**Uptime** — The binary state of whether a process is running. Not the same as availability or reliability. (Ch 2, 3)

**User** — Anyone or anything that relies on a service: humans, paying customers, other services, robots. (Ch 1)

**User journey** — A user-observable interaction with a service. Equivalent to an SLI conceptually; also called a KPI by business teams and an interface test by QA. (Ch 3)

**Validity** — How well data conforms to standards such as schemas or business rules. One of 7 data properties. (Ch 11)
