# Chapter 12: A Worked Example

## Core Idea
SLIs and SLOs for a realistic multi-component service (The Wiener Shirt-zel Clothing Company) demonstrating how user journey thinking maps to concrete SLO definitions for external customers, service-to-service interactions, and internal users.

## The Example Architecture

A dog clothing retail company with:
- CDN → Load balancers → Web app → Database + Cache
- Three microservices: (A) payment gateway, (B) inventory, (C) user analytics
- A data processing pipeline for marketing analytics
- Internal tooling: issue tracker, wiki, code repository

## SLOs as User Journeys: Three User Classes

### 1. External Customers (Finding and Browsing Products)

**Front page load:**
```
SLO: 99.9% of responses to our website will return a 2xx, 3xx, or 4xx HTTP code within 2,000 ms.
```
Notes:
- 4xx counted as "good" — user errors (bad URLs) shouldn't burn error budget.
- 99.9% = 1 in 1,000 page loads bad; average user clicks ~25 links/session → 1 in 40 customers experiences one bad response.
- Translates to ~43 minutes of downtime per month.

**Search results (more latency-tolerant):**
```
SLO: 99.8% of responses to product searches will return a 2xx or 3xx within 4,000 ms.
```
Notes:
- Looser target and higher latency threshold — users are more tolerant when service is "thinking."
- 4xx no longer counts as "good" — search returning no results should be 200, not 404.
- Higher latency budget because backend computation is involved.

### 2. Services as Users: Buying Products (Microservice A — Payment Gateway)

**The key constraint**: SLAs from payment vendors are almost always much stricter than what they can actually deliver. Don't set internal SLOs at the vendor's SLA level — measure actual vendor performance.

**Response time layering rule**: If user-facing SLO = 2,000 ms and it depends on two services, neither dependent service can individually promise 1,000 ms at any percentile — the math doesn't allow it.

```
Example SLO for payment microservice:
99.5% of checkout requests will receive a success or failure response within 1,500 ms.
```
(Stricter than the customer-facing SLO because the user-facing response depends on it.)

### 3. Internal Users (Marketing Analytics Pipeline)

Internal services serve humans too — apply data reliability concepts (freshness, completeness) from Chapter 11.

```
Example SLO: 99% of daily marketing reports will be available by 08:00 containing complete
data from the prior business day.
```

## Platforms as Services: Infrastructure SLOs

The container platform, the hardware layer, and the network all need SLOs — they constrain every upstream service's achievable SLO. Without platform SLOs, downstream services cannot realistically pick their own targets.

## Key Principles Demonstrated

- **Measure many things by measuring only a few**: Knowing a user can successfully search means the DB, cache, inventory microservice, web app, and load balancers are all functioning.
- **Different users, different SLOs**: External customers tolerate 4xx; internal services cannot.
- **Latency cascades downward**: User-facing latency SLO must exceed sum of dependent service latencies.
- **Start simple, add complexity later**: Begin with error rates and basic latency; add percentile modeling and tail SLOs as the service matures.

## Anti-patterns Illustrated

- **Single SLO for all page types**: Search result latency tolerance ≠ front page latency tolerance.
- **Relying on vendor SLA as your dependency SLO**: Vendors promise more than they deliver; measure actual performance.
- **No infrastructure SLOs**: Without them, downstream teams are flying blind on what's achievable.

## Key Takeaways

1. External users are more tolerant of latency than services-as-users.
2. Latency SLOs for dependencies must be significantly stricter than the consuming service's SLO.
3. User journey alignment means product managers' user journeys and engineers' SLIs are often the same thing in different language.
4. Start with one simple SLO, validate it, then layer in additional SLOs for specific user types.
5. Infrastructure layers need SLOs to enable the services above them to pick realistic targets.

## Connects To

- **Ch 3**: User journey = SLI concept demonstrated concretely here
- **Ch 4**: Dependency math applied to microservice chains
- **Ch 11**: Data reliability SLOs for the analytics pipeline
