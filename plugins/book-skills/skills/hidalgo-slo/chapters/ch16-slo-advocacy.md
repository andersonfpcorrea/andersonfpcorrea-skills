# Chapter 16: SLO Advocacy

## Core Idea
Scaling SLO adoption across an organization requires deliberate advocacy: building expertise, creating compelling artifacts, teaching others, and progressing through Crawl → Walk → Run phases. Technology is last; people and process come first.

## Prerequisites for SLO Advocacy

Before starting:
1. Leadership buy-in and executive sponsorship
2. Management agreement on time investment (full-time for months in large orgs)
3. Readiness for a horizontal role requiring communication, data analysis, training, and technical skills

## The Three Phases

### Crawl: Building the Foundation
1. **Do your research**: Become the organizational SLO expert. Build a working example.
2. **Prepare your sales pitch**: Tailor to audience — engineers care about alert fatigue and incident clarity; executives care about user satisfaction and revenue.
3. **Create supporting artifacts**:
   - One-page strategy document (leadership review required)
   - Two-page SLO definition overview for engineers
   - FAQ document
   - Step-by-step SLO definition guide
   - Step-by-step SLI instrumentation guide with code examples
   - Use case document from your pilot
4. **Run first training**: 30-minute overview; hands-on workshop with example service.
5. **SLO pilot**: Implement SLOs for a single, visible service.
6. **Spread the message**: Blog post, conference talk, all-hands presentation.
7. **Handle challenges**: Prepare for "we don't have time," "this won't work for our service," and other objections.

### Walk: Scaling to Early Adopters
1. Work with early adopters to implement SLOs for additional services.
2. Celebrate every success publicly — build confidence.
3. Create a library of case studies (documented results).
4. Scale training by training more trainers.
5. Scale communications via newsletters, forums, Slack channels.

### Run: Organizational Maturity
1. Share case study library externally (conference talks, blog posts).
2. Build a community of SLO experts within the organization.
3. Continuously improve training, tooling, and processes.
4. SLO review is now part of standard service launch checklist.

## Stakeholder Pitch Templates

**For engineers:**
"SLOs let you know if your service is actually working for users — not just if it's 'up.' Error budgets replace vague debates about shipping features with data-driven decisions. Your on-call burden drops when alerts are tied to real user impact."

**For executives:**
"SLOs give us a real-time measure of user satisfaction and service health. They let us identify exactly where reliability investments will have the highest business impact. If you can't measure it, you can't improve it."

## Documentation Artifacts to Create

| Artifact | Purpose | Length |
|---|---|---|
| One-page strategy | Why SLOs, what we'll accomplish, how | 1 page |
| SLO definition overview | What an SLO is, examples | 2 pages |
| FAQ | Common questions answered | As needed |
| SLO definition guide | Step-by-step for service teams | 3–5 pages |
| SLI instrumentation guide | How to collect SLI data in your monitoring platform | 5–10 pages + code |
| Use case / case study | Concrete example from your pilot | 2–3 pages |

## Handling Challenges

- **"We have too much backlog"**: Frame SLOs as reducing future backlog by fixing the right things.
- **"Our service is different"**: Help them think through which of the 13 data properties (Ch 11) or user journey patterns (Ch 3) apply.
- **"Management won't approve the time"**: You need executive sponsorship (prerequisite 1). Escalate if needed.

## Key Takeaways

1. SLO adoption is 80% organizational change, 20% technical work.
2. Crawl → Walk → Run: start with one team, get results, then scale.
3. Tailor every message to the audience's concerns and language.
4. Documentation outlasts training — invest in it proportionally.
5. Train more trainers; you cannot scale alone.

## Connects To

- **Ch 6**: Getting initial buy-in (prerequisite to advocacy)
- **Ch 13**: Building SLO culture (what you're advocating for)
- **Ch 15**: Discoverable SLOs (the infrastructure that makes advocacy sustainable)
