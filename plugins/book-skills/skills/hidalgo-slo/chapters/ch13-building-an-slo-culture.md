# Chapter 13: Building an SLO Culture

## Core Idea
Implementing SLOs technically takes days; changing how your organization thinks about reliability takes months to years. The six-step path to SLO culture requires patience, incremental wins, and an understanding that the goal is a living process, not a finished project.

## The Culture Spectrum

Without SLOs, teams swing between two failure modes:
- **Under-investment**: Alerts go ignored, customers report problems, technical debt accumulates.
- **Over-investment**: 100% test coverage, engineers always on call, zero downtime — but features ship at a crawl, users notice the slow pace, not the reliability.

SLOs are the gauge that reveals where you are on this spectrum and enables intentional positioning.

## The Six-Step Path

1. **Get buy-in** — Agreement that SLOs provide value before any implementation.
2. **Prioritize SLO work** — Get it on the roadmap; assign ownership; make it visible in sprints/OKRs.
3. **Implement your SLOs** — Define SLIs, set up monitoring, pick initial targets.
4. **Use your SLOs** — Alert on them, make decisions with error budget data, inform work with results.
5. **Iterate on your SLOs** — Adjust targets, add/remove SLIs, revisit regularly.
6. **Advocate for others** — Share learnings; help other teams start their journey.

## Strategies for Shifting Culture

- **Start small**: One SLO at current reliability level is better than zero SLOs or twenty SLOs.
- **Be patient**: Verbal commitments → time allocated → SLI data collected → first SLO defined. Each is a real win.
- **Engage your team**: SLOs must solve their problems, not impose new ones.
- **Reflect as you go**: Regularly assess whether changes are getting closer to "users and developers happy."

## Practical Implementation Guidance

**First SLO checklist:**
1. Create an SLO definition document (see Ch 15 / Appendix A for template).
2. Identify stakeholders who need to review and approve.
3. Debate: what is most important to measure? (Start with what users complain about.)
4. Debate: what is the right SLI? (Simple is better initially.)
5. Implement monitoring for the chosen SLI.
6. Debate: what is the right initial target? (Current performance is fine as a starting point.)

**The "do it yourself" approach**: Frame SLO work in terms of your own frustrations. "I've wasted 3 hours this week on false alarms — I want to fix this." Hard to argue against.

**The assignment approach**: Create tickets, pull into sprint, add to OKRs. Make it visible. Coach the assignee.

## When Culture Isn't There Yet

Signs of SLO culture dysfunction:
- SLOs are defined but ignored when error budget is exceeded.
- SLOs exist but are not discoverable by other teams.
- SLOs are set and never revisited.
- Only engineering has agreed; product and leadership haven't.

## Key Takeaways

1. SLO culture requires organizational change, not just technical implementation.
2. Six steps: buy-in → prioritize → implement → use → iterate → advocate.
3. The error budget policy (Ch 5) is the cultural acid test — if it's ignored, culture hasn't changed.
4. Small wins (one SLI, one SLO) matter and should be celebrated.
5. SLOs are most powerful when every team follows the process — isolated adoption limits the benefit.

## Connects To

- **Ch 6**: Getting buy-in (the first step of this path)
- **Ch 5**: Error budget policies that enable "use your SLOs"
- **Ch 14**: Iteration on SLO targets (step 5)
- **Ch 16**: Advocacy at scale (step 6)
