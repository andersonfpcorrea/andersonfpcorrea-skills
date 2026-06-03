# Chapter 19: SRE: Reaching Beyond Your Walls

## Core Idea
When you run a platform, reliability is a partnership with your customers. SRE practices must extend outward to customers/tenants; shared SLOs and joint monitoring are the mechanism.

## Frameworks Introduced

### Six Truths of Platform Reliability
1. Reliability is the most important feature.
2. Your users, not your monitoring, decide your reliability (user-perceived ≠ system-reported).
3. If you run a platform, reliability is a partnership with your customers.
4. Everything important eventually becomes a platform.
5. When your customers have a hard time, you have to slow down.
6. You will need to practice SRE with your customers.

### How to: SRE with Your Customers (5 Steps)
1. **SLOs and SLIs are how you speak**: establish common vocabulary with customers using SLOs; translate "it's slow" into "your p99 latency SLI was 2× above threshold."
2. **Audit the monitoring and build shared dashboards**: both parties must see the same data; eliminates "our monitoring says fine, yours says broken" arguments.
3. **Measure and renegotiate**: SLOs between platform and customer should be renegotiated as the platform evolves; don't let them stale.
4. **Design reviews and risk analysis**: participate in customer architecture reviews to identify reliability risks early.
5. **Practice, practice, practice**: joint game days and DR tests build trust and surface integration failures before incidents do.

## Key Concepts
- **Platform reliability partnership**: a platform provider who blames the customer's usage pattern for reliability failures has missed the point. The platform must absorb reasonable usage patterns.
- **Customer SLO dependency**: a customer's SLO is unavoidably bounded by the platform's SLO. If the platform promises 99.9%, the customer cannot sustainably promise 99.99%.
- **Shared dashboard**: the single most effective tool for aligning platform and customer on reliability status; eliminates "who's at fault" debates during incidents.

## Anti-patterns
- **"Our monitoring says green"**: system-centric view; user-centric view may be very different.
- **Platform SLOs without customer input**: platform decides what's "good enough" without understanding customer requirements; misalignment is guaranteed.
- **No joint DR testing**: integration failures surface in production incidents, not in controlled tests.

## Key Takeaways
1. Use SLOs as the language for customer conversations about reliability.
2. Build shared dashboards; both sides must see identical data.
3. Joint game days and DR tests are mandatory for platform-customer reliability.
4. Customer SLO is bounded by platform SLO; publish your platform SLO and help customers design accordingly.

## Connects To
- **Ch 2**: SLO/SLI vocabulary is the foundation for all customer communication in this chapter
- **Ch 18**: Engagement model extends to external customers, not just internal teams
