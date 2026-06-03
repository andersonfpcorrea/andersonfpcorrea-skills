# Chapter 15: Build Versus Buy and Return on Investment

## Core Idea
Build vs. buy is a false dichotomy — the optimal choice for most organizations is **buy and build**: purchase a vendor solution for core observability functionality, then build an internal observability team to customize it, manage vendor relationships, standardize naming, and write shared libraries.

## Frameworks Introduced
- **TCO (Total Cost of Ownership) analysis**:
  - Build: Visible costs (infrastructure) + hidden costs (3 extra engineers × $250–300K + recruiter fees ~$75K each + opportunity cost of core business cycles diverted).
  - Buy: Visible costs (vendor bill) + hidden costs (future pricing as usage grows, vendor lock-in from proprietary instrumentation, migration cost).
- **Real-world example**: ELK stack "free" solution → $80K/month hardware + 3 engineers × $300K + recruitment costs = >$2M/year. Commercial option was <$1M/year.
- **Buy-and-build model**: Internal observability team that doesn't build the tool but builds integrations, libraries, naming conventions, and vendor interface — extracting maximum value from the purchased solution.

## Key Concepts
- **Opportunity cost**: By choosing to build, what did you give up? Most companies are not in the business of building observability tools; engineering cycles diverted here have high opportunity cost.
- **Open source is "free as in puppies"**: Heidi Waterhouse (LaunchDarkly): maintenance, support, upgrading, and operating open source software is expensive even without licensing fees.
- **Vendor lock-in from proprietary agents**: Any time invested in vendor-specific instrumentation must be repeated when switching. OTel solves this.
- **OTel distros**: Vendor-specific layers on top of standard OTel that configure boilerplate without requiring proprietary agents. Keeps instrumentation portable.
- **Benefits of build**: Deep in-house expertise, custom integration with business systems, fully aligned to unique organizational needs.
- **Risks of build**: Product management expertise needed, delivery timeline risk, ongoing maintenance burden, high risk of low adoption.

## Mental Models
- High-performing organizations maximize engineer time on core business value; they use best-of-breed tools rather than building commodity infrastructure.
- An internal observability team focused on integration and abstraction layers — not on building core storage/querying — gets the benefits of both approaches.
- Price transparency demand: Any vendor unwilling to show detailed cost projections for your future usage growth should be treated with skepticism.

## Anti-patterns
- **Comparing vendor price to "free" open source**: Fails to account for engineering salaries, infrastructure, recruitment, and opportunity costs of the build path.
- **Vendor pricing that penalizes adoption**: Pricing schemes (per seat, per host, per query) that make increased curiosity and usage expensive undermine the value of observability.
- **Building without product management expertise**: Without a PM function, internal tools often deliver something engineers want but fail to meet the needs of the broader organization.

## Key Takeaways
1. Calculate the full TCO of "free" solutions including engineering time, infrastructure, recruitment, and opportunity costs.
2. The best observability solution for most teams is: buy a commercial product with strong APIs + build an internal team to create integrations and libraries.
3. Always instrument with OTel to preserve the ability to switch vendors without re-instrumentation.
4. Demand transparent pricing from vendors and model your likely future usage (data grows exponentially with adoption success).
5. Building your own is viable when your organization has strong product management, engineering bandwidth, and genuine differentiation needs.

## Connects To
- **Ch 7**: OTel instrumentation as the vendor-lock-in prevention strategy.
- **Ch 10**: Team adoption strategies reference the buy-first approach.
- **Ch 19**: Business case framework for justifying observability investment.
