# Google SRE Glossary

**Adaptive Throttling** — Client-side self-regulation: `rejection_prob = max(0, (requests − K×accepts) / (requests+1))`. Prevents backends from wasting resources rejecting quota-exceeded requests. (Ch 21)

**Alert** — Monitoring output requiring immediate human action. Distinct from tickets (days) and logs (no action). (Ch 1, 6)

**Availability (time-based)** — `uptime / (uptime + downtime)`. Use for single-machine services. (Ch 3)

**Availability (aggregate)** — `successful_requests / total_requests`. Google's preferred metric for distributed services. (Ch 3)

**B4** — Google's globe-spanning backbone network; software-defined networking (OpenFlow). (Ch 2)

**Bigtable** — Google's distributed NoSQL database; petabyte-scale; eventually consistent cross-datacenter replication. (Ch 2)

**Black-box monitoring** — Testing externally visible behavior as a user would see it. Best for paging on ongoing problems. (Ch 6)

**Blameless postmortem** — Incident record that assumes good intentions; focuses on systemic causes, not individual fault. (Ch 15)

**Borg** — Google's distributed cluster operating system; schedules tasks across machines, handles failures. (Ch 2)

**BNS (Borg Naming Service)** — Maps logical names (`/bns/<cluster>/<user>/<job>/<task>`) to IP:port. Stable despite task migrations. (Ch 2)

**Borgmon** — Google's time-series monitoring system; predecessor to Prometheus; scrapes `/varz` endpoints. (Ch 10)

**CAP Theorem** — A distributed system cannot simultaneously guarantee Consistency + Availability + Partition tolerance. (Ch 23)

**Canary** — A small subset of traffic/servers receiving a new release to detect issues before full rollout. (Ch 3, 8)

**Capacity planning** — Ensuring sufficient resources for projected demand; must include organic + inorganic growth. (Ch 1)

**Cherry picking** — Applying specific changes to a release branch without merging all mainline changes. (Ch 8)

**Chubby** — Google's distributed lock service; uses Paxos protocol; also stores BNS path→IP:port mappings. (Ch 2, 23)

**Criticality (RPC)** — Priority level assigned to RPCs: CRITICAL_PLUS, CRITICAL, SHEDDABLE_PLUS, SHEDDABLE. (Ch 21)

**Deadline propagation** — Passing remaining deadline through each RPC hop, preventing backends from doing work after client timeout. (Ch 21, 22)

**Error budget** — `1 − SLO`. The permitted unreliability per period. Shared currency between dev and SRE. (Ch 1, 3)

**Essential complexity** — Complexity inherent in the problem that cannot be removed. (Ch 9)

**Accidental complexity** — Complexity introduced by implementation choices; can be eliminated. (Ch 9)

**The Four Golden Signals** — Latency, Traffic, Errors, Saturation. The canonical SLIs for user-facing systems. (Ch 6)

**GC Death Spiral** — Java cascade: high load → more GC → less CPU → slower requests → more RAM → less cache → more GC. (Ch 22)

**GSLB (Global Software Load Balancer)** — Three-level load balancing: geographic DNS, user-service, RPC. (Ch 2)

**Graceful degradation** — Serving lower-quality but cheaper-to-compute responses under overload. (Ch 21, 22)

**Hermetic build** — Build that produces identical results regardless of machine, time, or environment. (Ch 8)

**Incident Commander (IC)** — Holds high-level state during incident; assigns roles; only person who may delegate. (Ch 14)

**Intent-Based Capacity Planning** — Encoding service requirements (SLOs, constraints) and using a solver to generate allocation plans. (Ch 18)

**Jupiter** — Google's datacenter internal network fabric; up to 1.3 Pbps bisection bandwidth. (Ch 2)

**Lame duck state** — Backend task that is alive but asking clients to stop sending new requests; enables graceful drain. (Ch 20)

**Launch Coordination Engineering (LCE)** — Dedicated SRE consulting team for product launches; maintains launch checklist. (Ch 27)

**Load shedding** — Dropping traffic when approaching overload to prevent crash-loop. (Ch 22)

**Machine** — A piece of hardware (or VM). Distinct from "server" (which is software). (Ch 2)

**MTBF (Mean Time Between Failures)** — How long between failures. Improved by better testing. (Ch 17)

**MTTR (Mean Time to Repair)** — How long to restore service after failure. Playbooks improve this ~3×. (Ch 1)

**N+2 redundancy** — Minimum: N tasks for load + 1 for rolling update + 1 for machine failure. (Ch 2)

**Operational overload / Ops mode** — Team headcount scales linearly with service size due to insufficient automation. (Ch 30)

**Outalator** — Google's outage tracking system; groups alerts into incidents; enables aggregate analysis. (Ch 16)

**Overhead** — Administrative work not directly tied to running a service (meetings, HR). Not toil. (Ch 5)

**PRR (Production Readiness Review)** — SRE's gate review before accepting production responsibility for a service. (Ch 32)

**Paxos** — The consensus algorithm underlying Chubby and other distributed systems. (Ch 2, 23)

**Playbook** — Documented on-call procedures; improves MTTR ~3× vs. "winging it." (Ch 1)

**Postmortem** — Written record of an incident: impact, timeline, root causes, follow-up actions. (Ch 15)

**Progressive rollout** — Phased release: one cluster → exponential expansion → all clusters. (Ch 8)

**QPS (Queries Per Second)** — A poor capacity metric; use CPU consumption instead (request cost varies). (Ch 21)

**Rapid** — Google's automated release system; blueprints configure build + test + deploy workflows. (Ch 8)

**Server** — A piece of software that implements a service. Distinct from "machine" (hardware). (Ch 2)

**Sisyphus** — Google's general-purpose rollout automation framework; Python extensible. (Ch 8)

**SLA (Service Level Agreement)** — Contract with explicit consequences (financial penalties) for missing SLOs. (Ch 4)

**SLI (Service Level Indicator)** — Quantitative measure of service quality. Examples: latency p99, error rate, availability. (Ch 4)

**SLO (Service Level Objective)** — Target value for an SLI. Form: `SLI ≤ target`. (Ch 4)

**Spanner** — Google's globally distributed SQL database; real strong consistency across datacenters. (Ch 2)

**Split-brain** — Two nodes each believe they are the sole master; causes data corruption. Result of heartbeat-based leader election. (Ch 23)

**Synthetic variables** — Borgmon's auto-created metrics per target: DNS resolution, collection success, health check, collection time. (Ch 10)

**Toil** — Operational work that is: manual, repetitive, automatable, tactical, no enduring value, O(n) with service growth. (Ch 5)

**Toil cap (50%)** — SRE ops work ≤ 50% of total time. Excess overflows back to dev team. (Ch 1, 5)

**varz** — Google's standard metrics exposition format: `GET /varz` returns key-value pairs. Scraped by Borgmon. (Ch 10)

**VIP (Virtual IP)** — Single IP address served by many machines via L4 load balancing; enables health checking and transparent failover. (Ch 19)

**White-box monitoring** — Monitoring based on internals (logs, metrics endpoints). Required for debugging; detects imminent problems. (Ch 6)

**Wheel of Misfortune** — Training exercise: new SREs reenact a historical postmortem with assigned roles. (Ch 15, 28)

**Zero MTTR** — Bug caught by pre-production testing; never reaches users. (Ch 17)
