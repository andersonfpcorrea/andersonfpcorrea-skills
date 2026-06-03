# Chapter 15: Discoverable and Understandable SLOs

## Core Idea
An SLO that cannot be found or understood is worthless. SLOs must be discoverable by anyone who might depend on them and understandable by engineers, product teams, and executives without specialized knowledge.

## Understandability

### SLO Definition Documents
Use a consistent template for every SLO (see Appendix A). Elements:
- Service name and overview
- SLI definition (plain English + measurement method)
- SLO target percentage and time window
- Rationale for target choice
- Revisit schedule
- Error budget policy reference
- Owners and stakeholders
- External links (dependencies, related dashboards)

### Phraseology Rules
- Write the SLI as a sentence anyone can understand: "When customers search for a product, 99.8% of searches return results within 4 seconds."
- Never describe the measurement method in the SLO definition; describe the user experience.
- Use plain percentages, not "nines." "99.9%" is clearer than "three nines" to non-experts.
- Consistency across all SLOs in an organization enables anyone to quickly scan and compare.

## Discoverability

### Document Repositories
All SLO definitions should live in a single canonical location:
- A wiki or internal documentation site
- Version controlled alongside service code
- Linked from service README files
- Indexed and searchable

The worst outcome: SLO document exists but no one can find it when needed (during an incident, when planning dependencies, when setting one's own SLOs).

### Discoverability Tooling
- Service catalogs or service registries should include SLO metadata.
- CI/CD pipelines should fail if a new service deploys without an SLO definition.
- Dependency analysis tools should surface the SLOs of services you depend on.

### SLO Reports
Regular reports (weekly, monthly, quarterly) sent to appropriate stakeholders:
- Engineers: error budget status, burn rate trends
- Product: reliability trend relative to feature deployment cadence
- Leadership: aggregate service health, user impact summary

### Dashboards
- Every SLO should have a corresponding dashboard showing current status.
- Dashboard should show: current SLO compliance, error budget remaining, burn rate over time.
- Dashboards should be accessible to everyone who is a stakeholder, not just the owning team.

## Consistency Is Prerequisite to Discoverability

If different teams define SLOs using different formats, tools, or terminology, the aggregated view becomes impossible. An SLO culture requires organizational alignment on:
- What constitutes a "good" event vs. a "bad" event (definition is in the SLO document)
- What the time window means
- How error budgets are calculated and displayed

## Anti-patterns

- **SLOs in engineers' heads**: Undocumented targets are not SLOs.
- **Inconsistent definitions**: If "latency" means p99 for one team and p50 for another, cross-team comparison is meaningless.
- **Reports only to leadership**: Engineers need the data to act on it; leadership needs it to understand business impact.
- **Living in someone's personal wiki space**: SLO documents must be in publicly accessible, durable locations.

## Key Takeaways

1. An SLO must be written in plain English that a non-engineer can understand.
2. Templatized SLO definition documents ensure consistency and discoverability.
3. SLOs should be findable by anyone who might depend on the service without asking a human.
4. Dashboards and reports make SLO data actionable for different audiences.
5. Organizational consistency in SLO definitions enables cross-team reliability conversations.

## Connects To

- **Appendix A**: SLO definition template
- **Ch 13**: Cultural practices for SLO adoption
- **Ch 17**: Reliability reporting built on discoverable SLOs
