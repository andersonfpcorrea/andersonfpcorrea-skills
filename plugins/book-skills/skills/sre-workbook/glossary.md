# Glossary — The Site Reliability Workbook

**ADKAR** — Change management model: Awareness, Desire, Knowledge, Ability, Reinforcement. Used to diagnose why SRE adoption stalls. (Ch 21)

**Anycast** — Single IP address advertised from multiple network locations; BGP routes traffic to the nearest point of presence. (Ch 11)

**Blameless postmortem** — Post-incident review that identifies systemic causes without attributing fault to individuals. (Ch 10)

**Burn rate** — How fast a service consumes its error budget relative to the SLO period. Burn rate 1 = exactly exhausting budget at period end. (Ch 5)

**Canary** — The subset of production receiving a new release (typically 1–5%); compared against the "control" (unchanged production). (Ch 16)

**Checkpointing** — Recording the last successfully processed record in a pipeline so reruns start from that point, not the beginning. (Ch 13)

**Coverage SLI** — For batch pipelines: % of jobs processing above a target amount of data. (Ch 2, Ch 13)

**Control (canary deployment)** — The portion of production running the stable version; baseline for canary comparison. (Ch 16)

**Correctness SLI** — % of pipeline output records that are correct. (Ch 2, Ch 13)

**Detection time** — Time from when an event starts until an alert fires. (Ch 5)

**DevOps** — Philosophy and culture removing dev/ops silos; SRE is one implementation. (Ch 1)

**Durability SLI** — % of records written that can be successfully read. (Ch 2)

**Error budget** — 100% minus the SLO; the allowed amount of unreliability in a window. (Ch 2)

**Error budget policy** — Formal document committing the org to halt feature work when the error budget is exhausted. (Ch 2, Appendix B)

**Feature flag** — Configuration switch that enables/disables a feature independently of binary releases; enables instant rollback of a feature. (Ch 16)

**Freshness SLI** — % of data records updated more recently than a time threshold. (Ch 2, Ch 13)

**Gall's Law** — "A complex system that works is invariably found to have evolved from a simple system that worked." (Ch 7)

**Hermeticity** — Property of a configuration evaluation that depends only on its inputs, never on external state at evaluation time. (Ch 15)

**Hyrum's Law** — Over time, all observable behaviors of an API become depended upon by its users. (Ch 7)

**ICS (Incident Command System)** — Emergency response framework adapted for software incidents; origin of IC/OL/CL roles. (Ch 9)

**IC (Incident Commander)** — Person commanding and coordinating an incident response; does not debug. (Ch 9)

**IMAG (Incident Management at Google)** — Google's adaptation of ICS; defines IC, OL, CL roles and 3Cs. (Ch 9)

**Idempotency** — Property of an operation that produces the same result whether run once or N times. Required for pipeline writes and config application. (Ch 13)

**Jsonnet** — Hermetic, JSON-superset configuration DSL; used as the worked example for config specifics. (Ch 15)

**Latency SLI** — % of requests faster than a defined time threshold. (Ch 2)

**LCE (Launch Coordination Engineering)** — Specialist SREs who review major launches for reliability. (Ch 20)

**Load shedding** — Explicitly rejecting requests when a service is near capacity, rather than queuing them indefinitely. (Ch 11)

**Maglev** — Google's software load balancer using consistent hashing; survives individual LB restarts without dropping connections. (Ch 11)

**Multiwindow, multi-burn-rate alerting** — Method 6 from Ch 5; recommended alerting approach; uses two windows per burn rate to ensure budget is still actively being consumed. (Ch 5)

**NALSD (Non-Abstract Large System Design)** — Iterative design methodology that starts with single-machine feasibility and adds distribution only at actual bottlenecks. (Ch 12)

**On-call** — Period during which an engineer is available to respond to production incidents; generates pager load that must be bounded. (Ch 8)

**OL (Operations Lead)** — Incident response role; applies operational tools to mitigate/resolve the incident. Reports to IC. (Ch 9)

**CL (Communications Lead)** — Incident response role; provides status updates; manages external communications. Reports to IC. (Ch 9)

**Precision (alerting)** — % of fired alerts corresponding to significant events. Low precision = alert fatigue. (Ch 5)

**PRR (Production Readiness Review)** — Checklist SRE completes before taking on-call for a new service. (Ch 18)

**ProdEx (Production Excellence) Review** — Regular leadership review of SRE team health using a defined rubric. (Ch 20)

**Quality SLI** — For request-driven services that degrade gracefully: % of responses served in undegraded state. (Ch 2)

**Recall (alerting)** — % of significant events that triggered an alert. Low recall = missed outages. (Ch 5)

**Replication toil** — Configuration toil caused by duplicated values across many config files. (Ch 15)

**Reset time** — How long after an event ends the alert keeps firing. Long reset = confusion. (Ch 5)

**Rolling window** — SLO measured over the most recent N days (typically 28 or 30) vs. a fixed calendar period. (Ch 2)

**SLA (Service Level Agreement)** — Business contract that triggers compensation when users are unhappy; distinct from SLO. (Ch 2)

**SLI (Service Level Indicator)** — Metric that indicates service quality; recommended form: good events / total events (0–100%). (Ch 2)

**SLI implementation** — SLI specification + a specific measurement method. (Ch 2)

**SLI specification** — The service outcome that matters to users, independent of how it is measured. (Ch 2)

**SLO (Service Level Objective)** — Target level of reliability; above it, users are happy; below it, users complain. (Ch 2)

**SRE (Site Reliability Engineering)** — Software engineering approach to operations; treats operations as a software problem, driven by SLOs. (Ch 1)

**3Cs of incident management** — Coordinate, Communicate, Control. (Ch 9)

**Toil** — Repetitive, manual, automatable, reactive operational work that lacks enduring value and scales with the system. (Ch 6)

**VALET** — SLI taxonomy: Volume, Availability, Latency, Errors, Tickets. (Ch 3)

**War room** — Shared real-time channel (IRC, Slack, video) where all incident responders gather for centralized communication. (Ch 9)
