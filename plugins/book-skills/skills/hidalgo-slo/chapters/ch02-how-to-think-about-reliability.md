# Chapter 2: How to Think About Reliability

## Core Idea
Reliability is not synonymous with availability or uptime. Users determine what reliable means for your service, and past performance creates implicit agreements — even without formal SLAs.

## Frameworks Introduced

- **Implied Agreements**: The level of reliability users have experienced becomes their expectation going forward, regardless of whether you've documented anything. Reducing reliability below what users experienced is a breaking change.
- **Hyrum's Law**: "With a sufficient number of users of an API, it does not matter what you promise in the contract: all observable behaviors of your system will be depended on by somebody." Your reliability requirements may be wider than you realize.
- **The Cost Curve of Reliability**: Reliability costs grow super-linearly. Moving from 99.9% to 99.95% is a 2× change in unreliability; moving from 99.95% to 99.99% is a 5× change — 2.5× harder than the previous step.

## Key Concepts

- **Uptime**: The binary state of whether a process is running.
- **Availability**: Whether a service can respond to requests from users.
- **Reliability**: Whether a service performs the duties it was designed for.
- **Past performance as baseline**: Start SLO discussions by understanding current performance; users expect future performance to match the past.
- **Reliability engineering**: A decades-old discipline (pre-dating software) concerned with designing complex systems that fail acceptably. SLOs are software's application of this discipline.

## Mental Models

- Reliability = "Is this service doing what users need it to do?" — not "Is the binary running?"
- Use the streaming service example: Reliability includes video quality, audio sync, correct content, search results, billing accuracy — not just "is it up?"
- Cost of reliability grows faster than linearly; the last 0.01% costs many times more than the previous 0.1%.
- SLOs don't have a single right answer — they're living targets informed by user feedback, engineering constraints, and business context.

## Anti-patterns

- **Conflating reliability with availability**: You can have 100% uptime and still be unreliable (returning wrong data, slow responses).
- **Ignoring implied agreements**: Users will notice and react if reliability drops, even without formal SLAs.
- **Assuming 99.99% is "realistic"**: At 99.99% over 30 days, on-call humans must respond in seconds — this has enormous human cost.
- **Believing surveys tell the truth about reliability**: Self-selected survey respondents give noisy, biased data. Interviews and actual measurement are better.

## Key Takeaways

1. Users — not operators — define what reliability means for a service.
2. Implicit agreements form over time; violating them loses users even without an SLA.
3. Perfect reliability is impossible and undesirable — target "good enough for users."
4. The cost of reliability grows super-linearly; chasing the last 9 is often not worth it.
5. SLOs are a framework for better data and better decisions, not a rigid ideology.

## Connects To

- **Ch 4**: How to choose the right target percentage
- **Ch 14**: Listening to users when SLOs need to evolve
- **Hyrum's Law**: Why your reliability surface is larger than you think
