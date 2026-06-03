# Chapter 3: Developing Meaningful Service Level Indicators

## Core Idea
SLIs are the most important part of the entire Reliability Stack. A meaningful SLI measures your service from the perspective of users — not internal system resources — and should be expressible as a plain sentence anyone can understand.

## Frameworks Introduced

- **User Journey as SLI**: An SLI is fundamentally a user journey — the same concept product managers call a KPI and QA teams call an interface test. Alignment already exists across functions; just align the language.
- **Measuring Many Things by Measuring Only a Few**: A single user-journey measurement often captures multiple internal metrics simultaneously. If you can verify a user received the correct data, you've also verified the service is up, available, responsive, and returning good responses.
- **The Six Questions for Request/Response APIs** (minimum checklist):
  1. Is the service up?
  2. Is the service available?
  3. Is the service responsive (within acceptable latency)?
  4. Are there enough good responses vs. errors?
  5. Are responses in the correct data format?
  6. Is the correct data being returned?

## Key Concepts

- **Meaningful SLI**: "A metric that tells you how your service is operating from the perspective of your users." Not CPU usage or memory — user-observable behavior.
- **Binary SLI outcome**: A good SLI produces a "good" or "bad" result per event, enabling simple percentage calculation.
- **User journey alignment**: Engineering SLIs, product user journeys, business KPIs, and QA interface tests often describe the same thing in different languages.
- **The difference between internal metrics and SLIs**: Internal metrics (CPU, error count) may be necessary for debugging but are not SLIs. SLIs must reflect the user experience.

## Reference Tables

**Example interactions and components for a retail website:**

| Interaction | Components |
|---|---|
| Visit home page | Load balancers, web app, cache |
| Browse items | Load balancers, web app, database |
| Add/remove cart item | Load balancers, web app, cart service, cache, database |
| Edit shipping address | Load balancers, web app, user service, database |
| Purchase item | Load balancers, web app, payment gateway, third-party vendor |

**Example SLI statements:**
- Simple API: "The 95th percentile of requests to our service will be responded to with the correct data within 400 ms."
- Complex service login: "When clients external to our network provide a valid username and password combination, the site will reload in a logged-in state."

## Mental Models

- Use "measure many things by measuring only a few" — measuring correct data at the edge implies up, available, responsive, correct format.
- Start from the outermost edge of your service — what does the user's request look like entering and leaving?
- Imperfect SLIs are fine; measurements that somewhat reflect users are better than measurements that don't.
- Alert only on SLIs. Everything else (logs, resource metrics) is for debugging, not paging.

## Anti-patterns

- **Alerting on internal metrics**: CPU usage, memory, error counts may be correlated to user experience but are poor proxies. Use them for debugging, not paging.
- **Treating uptime as an SLI**: A service can be "up" and simultaneously failing to serve users correctly.
- **Confusing running state with availability**: Services can be running but not reachable.
- **One-and-done monitoring**: Setting up dashboards once and never revising them as services evolve.

## Key Takeaways

1. SLIs must be defined from the user's perspective — not the system's.
2. A good SLI produces a binary good/bad outcome per event, enabling percentage math.
3. You can measure many things by measuring only a few, if you measure at the right level.
4. Write every SLI as a plain English sentence understandable by all stakeholders.
5. Alignment on what to measure often already exists across engineering, product, and QA — just harmonize the language.

## Connects To

- **Ch 7**: How to technically implement SLI measurement
- **Ch 9**: Statistics for handling low-QPS or noisy SLI data
- **Ch 12**: Worked example of SLIs as user journeys
- **Ch 15**: Making SLIs discoverable and understandable
