# Patterns — Implementing Service Level Objectives

## Pattern: The Reliability Stack

**When to use**: Any service that has users and needs a structured approach to reliability.

**How**:
1. Develop SLIs that measure service behavior from the user's perspective.
2. Set SLO targets that correlate with user happiness.
3. Calculate error budgets from the SLO target and time window.
4. Use error budget status to drive decisions about features vs. reliability work.

**Trade-offs**: High value; requires buy-in across engineering and product. Takes time to get right. SLIs require new measurement infrastructure.

---

## Pattern: User Journey as SLI

**When to use**: When defining what to measure for a new or existing service.

**How**:
1. Enumerate user interactions (browsing, purchasing, logging in).
2. For each interaction, ask: "What does success look like for the user?"
3. Express success as a binary outcome per event.
4. Design measurement to capture that binary outcome at the user-facing layer.

**Trade-offs**: More meaningful than internal metrics. Can be complex to instrument end-to-end. Imperfect measurement is still better than no user-oriented measurement.

---

## Pattern: Measuring Many Things by Measuring Only a Few

**When to use**: When a service has many internal components but a user journey spans them.

**How**:
- Identify the highest-level user-observable outcome.
- Verify that measuring it implies verifying the components below it.
- Example: verifying "user receives correct search results" implies load balancer, web app, database, and cache are all functioning.

**Trade-offs**: Reduces measurement overhead. May miss internal degradation before it reaches the user-facing layer. Complement with component-level monitoring for debugging.

---

## Pattern: Events-Based Error Budget

**When to use**: High-QPS services where event counts are large and stable. Also best for burn rate calculations.

**How**:
```
allowed_failures = (1 - SLO_target) × total_events
remaining_pct = (allowed_failures - actual_failures) / allowed_failures
```
Alert when remaining_pct drops below a threshold.

**Trade-offs**: Simple math. Breaks down for low-QPS services (single failures skew percentages). Events-based burn rate calculations are cleaner.

---

## Pattern: Time-Based Error Budget

**When to use**: Services with variable traffic, low QPS, or when human communication about "how much time remains" is more important than raw percentages.

**How**:
```
total_seconds = window_seconds
budget_seconds = (1 - SLO_target) × total_seconds
remaining_seconds = budget_seconds - bad_seconds
```
Communicate as "43 minutes of error budget remaining."

**Trade-offs**: More complex to calculate. Better for human decision-making. Better for availability-type SLOs than for event-based latency SLOs.

---

## Pattern: Two-Alert SLO Alerting (Fast + Slow Burn)

**When to use**: Any service with an SLO and error budget; replaces all threshold-based alerting.

**How**:
- **Fast burn**: Alert when X% of budget consumed in Y short window. Example: 1% in 1 hour → page.
- **Slow burn**: Alert when X% consumed in Y longer window. Example: 10% in 3 days → ticket.
- Add deduplication to prevent double-paging for the same event.

**Trade-offs**: Requires stateful monitoring. Old threshold alerts cannot be immediately removed — parallel-run transition needed for brownfield. Tighter SLOs (>99.95% on monthly windows) leave insufficient response time for human action.

---

## Pattern: Error Budget Policy

**When to use**: Before the first SLO is deployed; defines the team's agreed response to various burn levels.

**How**:
1. Define owners and stakeholders.
2. Specify action at each budget level (e.g., "at 20% burned: risk review; at 50%: pause risky releases; at 100%: pivot to reliability sprint").
3. Get sign-off from all stakeholders.
4. Document in the SLO definition document.
5. Review and revise after the first time it's invoked.

**Trade-offs**: Requires organizational buy-in. Without it, error budget data goes unused. The first invocation is the cultural test.

---

## Pattern: Compound Reliability Analysis

**When to use**: Setting SLO targets for a service with multiple dependencies.

**How**:
```python
# N components each at reliability r
system_reliability = r ** N
# Example: 40 components at 99.9%
0.999 ** 40 = 0.9608  # ~96% composed reliability
```
Use this to reality-check whether a proposed SLO target is achievable given dependencies.

**Trade-offs**: Assumes independence between component failures (often not true). Use as a lower bound. Actual composition math (parallel paths, retries) can be more favorable.

---

## Pattern: Multi-Datacenter Independence for SLO Improvement

**When to use**: When a single-datacenter service cannot meet the required SLO.

**How**:
```
# Two independent DCs each at availability p:
P(both fail) = (1 - p)^2
```
Two DCs at 99% availability → composed system offers 99.99% — two orders of magnitude better.

**Trade-offs**: Only valid when DC failures are truly independent (different power, network, geographic region). Same-DC correlated failures (shared power, shared network, shared software push) destroy this calculation. Cross-region is required for true independence.

---

## Pattern: Percentile-Layered Latency SLOs

**When to use**: Latency-sensitive services with long-tail distributions.

**How**:
Define separate SLOs at multiple percentiles:
```
1. P95 of all requests completes within 2,000 ms — 99.9% of the time
2. P98 of all requests completes within 2,500 ms — 99.9% of the time
3. P99 of all requests completes within 4,000 ms — 99.9% of the time
```
Each catches degradations in a different part of the distribution.

**Trade-offs**: Three SLOs instead of one. More alerting complexity. But discarding the top 5% misses degradations that users will notice. Each SLO has its own error budget — monitor all three.

---

## Pattern: Aspirational SLO

**When to use**: Current reliability is poor; users are unhappy; you need to drive improvement.

**How**:
1. Set a target better than current performance.
2. Let error budget burn immediately serve as a mandate for reliability work.
3. As reliability improves, tighten the target incrementally.
4. Document clearly that the SLO is aspirational.

**Trade-offs**: Constantly burning error budget can alarm stakeholders unless context is communicated. Use only when there's an explicit improvement plan.

---

## Pattern: SLO Definition Document

**When to use**: For every SLO at every service. Required for discoverability, accountability, and revision tracking.

**How**:
Include: service name/overview, SLI plain-English definition, SLO target and window, rationale, revisit schedule, error budget policy reference, owners/stakeholders, external links.

**Trade-offs**: Documentation overhead. Templatization reduces this significantly. The alternative (undocumented SLOs) is worse — they degrade, diverge, and become invisible.

---

## Pattern: Crawl → Walk → Run SLO Advocacy

**When to use**: Scaling SLO adoption from one team to an organization.

**How**:
- **Crawl**: Educate yourself, create artifacts, run first training, complete one pilot SLO.
- **Walk**: Help early adopters, build case studies, train more trainers.
- **Run**: Community of experts, external sharing, SLOs in standard launch checklists.

**Trade-offs**: Requires dedicated advocate role, especially in large orgs. Executive sponsorship essential. Without it, SLO culture stays isolated to the first team.
