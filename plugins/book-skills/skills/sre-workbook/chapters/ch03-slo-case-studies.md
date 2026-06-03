# Chapter 3: SLO Engineering Case Studies

## Core Idea
Two real adoption stories (Evernote and The Home Depot) show that SLO implementation is a social and organizational change as much as a technical one; both used different starting points and arrived at sustainable SLO cultures.

## Frameworks Introduced
- **VALET (Home Depot SLO taxonomy)**: Five SLI categories any service can map to:
  - **V**olume: transactions per second or requests handled
  - **A**vailability: % of requests served successfully
  - **L**atency: % of requests completed within time threshold
  - **E**rrors: % of requests returning errors
  - **T**ickets: number of support/manual-intervention tickets generated
  VALET provides a vendor-neutral taxonomy that applies to both online services and batch applications.

## Key Concepts
- **SLO wall**: the boundary between a platform provider's SLO and a customer-facing SLO; must be made explicit to avoid blame when something goes wrong
- **Greenfield SLO**: starting from scratch — best opportunity; start conservative and tighten
- **SLO evangelization**: SRE teams must actively sell SLOs to product/management before they become trusted decision tools

## Evernote Story
- Adopted SRE model after rapid growth made ad-hoc operations unsustainable.
- First SLOs were availability-only; latency and freshness added iteratively.
- Key lesson: SLOs require executive sponsorship to become real decision-making levers; without it, they remain KPIs.
- Breaking the "SLO wall" with their cloud provider required publishing joint dashboards showing end-to-end user experience.

## Home Depot Story
- "SLO Culture Project": bottom-up initiative that gained management buy-in by demonstrating value.
- Used VALET as the taxonomy to normalize SLI types across very different services (web, batch, store systems).
- Automated VALET data collection using monitoring pipelines — manually collecting SLI data creates its own toil.
- Applied VALET to batch applications: volume = records processed per run; coverage = % records processed per job.
- Testing environments got SLOs — "SLOs in testing" catches regressions before production.

## Anti-patterns
- **SLO without teeth**: having an SLO but no error budget policy means it's just a reporting metric, not a decision tool.
- **Too many SLOs too fast**: Home Depot learned to phase the rollout; overwhelming teams with SLO requirements kills adoption.
- **Manual SLI collection**: creates toil and is abandoned; automate measurement from day one.

## Key Takeaways
1. VALET gives any team a starting vocabulary for SLIs; adapt it to your context.
2. SLO adoption is organizational change management — success requires executive buy-in, not just good tooling.
3. Automate SLI data collection immediately; manual collection is toil that kills programs.
4. Apply SLOs to batch pipelines using freshness + coverage + correctness rather than availability.

## Connects To
- **Ch 2**: The SLI types and measurement approaches built on here
- **Ch 6**: Toil from manual SLI collection is what VALET automation eliminates
- **Ch 21**: Organizational change management for SRE adoption
