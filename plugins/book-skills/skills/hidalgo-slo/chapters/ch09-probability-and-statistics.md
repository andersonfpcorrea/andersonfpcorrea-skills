# Chapter 9: Probability and Statistics for SLIs and SLOs

## Core Idea
When you can't directly measure what you need, probability and statistics let you reason about it. Two key problems: (1) what SLO can a system theoretically provide? (2) Is the SLO being met right now? These require different tools.

## Frameworks Introduced

### Bernoulli Trials (binary events)
Every request is a Bernoulli trial: success (s) or failure (f), with probability p of success. An SLI ratio is just the long-run probability p of success across many Bernoulli trials.

### The Binomial Distribution
For n independent Bernoulli trials with success probability p:
```
P(k successes in n trials) = C(n,k) × p^k × (1-p)^(n-k)
```
Use case: What is the probability of getting exactly k failures in n requests?

### The Geometric Distribution
For k trials until first failure:
```
P(failure on kth trial) = (1-p)^(k-1) × p
```
Expected value = 1/p (mean number of successful requests before first failure).

### The Poisson Distribution
For rare events that occur at a constant average rate λ over a period:
```
P(k events) = e^(-λ) × λ^k / k!
```
Use cases: modeling the number of failures per time interval; durability calculations.

**Key theorem**: Sum of independent Poisson distributions is Poisson. If failures arrive at rate λ₁ and λ₂ independently, total failure rate = λ₁ + λ₂.

### Multi-Data-Center Availability Math
If each data center has availability p, two independent data centers fail together with probability:
```
P(both fail) = (1-p)^2
```
Therefore: two data centers each at 99% availability → joint failure = (0.01)^2 = 0.0001 → system can offer 99.99% SLO while each component is only 99%.

**Warning**: This only holds if data center failures are truly independent (no correlated failures from shared power, network, or software).

### Bayesian Inference for SLO Targets
When historical data is limited:
- **Maximum Likelihood Estimation (MLE)**: Best fit to observed data. Can overfit to small samples.
- **Maximum a Posteriori (MAP)**: Incorporates a prior belief. More robust with sparse data.
- **Bayesian inference**: Full posterior distribution over possible true reliability levels.

Use Bayesian methods when: the service is new, traffic is low, or you want to express uncertainty about the SLO rather than a point estimate.

### Statistical Distributions for Latency

**Exponential distribution**: Models time between events in a Poisson process.
```
P(wait > t) = e^(-λt)
```
Memoryless property: knowing you've already waited t seconds gives no information about how much longer you'll wait.

**Queueing latency**: In a system with arrival rate λ and service rate μ (where λ < μ):
```
mean_queue_wait = λ / (μ(μ - λ))
```
As λ approaches μ (system approaches capacity), wait times grow without bound. Latency SLOs must account for this.

## SLI Example: Low QPS Problem

When a batch service has 1 request/minute and a 5-minute measurement window, 1 failure = 80% availability — below any reasonable SLO.

The solution: use binomial probability to determine whether the observed failure rate is statistically consistent with the claimed availability. With high actual availability (p = 0.9999) and only 5 samples, observed failures don't necessarily indicate a real problem. Use larger time windows or Bayesian confidence intervals.

## SLI Example: Durability

Durability = the probability that a stored object survives for a given period. If each replica has independent failure probability p_fail:
```
P(all N replicas fail) = p_fail^N
```
Three replicas each with 99.9% annual survival = P(data loss) = (0.001)^3 = 10^-9 per year.

**Key warning**: "Independent" replicas in the same data center are NOT independent — correlated failure modes exist (power, fire, software bugs). Cross-region replication is needed for true independence.

## Reference: Proofs (Appendix B)

The book provides formal proofs for 7 theorems covering:
- Poisson composition
- Binomial PMF derivation
- Geometric expected value
- Exponential CDF
- Queue latency formulas
- Bayesian posterior for availability estimates
- Durability with independent replicas

## Key Takeaways

1. Two independent replicas at 99% can compose to 99.99% — but only if failures are truly independent.
2. Low-QPS services require wider windows or Bayesian statistics; raw percentage calculations are unreliable.
3. Use Bayesian methods when data is sparse; MLE when you have sufficient historical samples.
4. Latency SLOs must account for queueing effects — as utilization approaches capacity, latency grows unboundedly.
5. Durability requires cross-region replication; same-DC replicas share correlated failure modes.

## Connects To

- **Ch 4**: Basic statistics (mean, percentiles) for choosing initial SLO targets
- **Ch 7**: Measurement infrastructure that provides the data for these calculations
- **Ch 8**: Burn rate calculations that this chapter's math underlies
- **Appendix B**: Formal proofs for all key theorems
