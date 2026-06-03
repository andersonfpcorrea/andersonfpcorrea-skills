---
name: observability-engineering
description: "Knowledge base from \"Observability Engineering: Achieving Production Excellence\" by Charity Majors, Liz Fong-Jones & George Miranda. Use when applying their frameworks for structured events, distributed tracing, OpenTelemetry instrumentation, SLO/error-budget design, core analysis loop debugging, sampling strategy, telemetry pipelines, or observability maturity assessment."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, framework name, or chapter number — e.g. 'SLO burn alerts', 'sampling', 'ch07', 'core analysis loop']"
---

# Observability Engineering: Achieving Production Excellence
**Authors**: Charity Majors, Liz Fong-Jones & George Miranda | **Pages**: ~321 | **Chapters**: 22 | **Generated**: 2026-06-02

## How to Use This Skill

- **Without arguments** — load the core frameworks and mental models below
- **With a topic** — ask about `SLO`, `sampling`, `OpenTelemetry`, `core analysis loop`, etc.; I find the relevant chapter
- **With chapter** — ask for `ch05` or `ch13`; I load that chapter file
- **Browse** — ask "what chapters do you have?" to see the full index

For details beyond the core frameworks below, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md` files in this skill.

---

## Core Frameworks & Mental Models

### 1. The Observability Definition (Ch 1) — the north star

> "A measure of how well you can understand and explain any state your system can get into, no matter how novel or bizarre. You must be able to comparatively debug that bizarre or novel state across all dimensions of system state data, and combinations of dimensions, in an ad hoc iterative investigation, without being required to define or predict those debugging needs in advance."

**Litmus test**: Can you debug unknown-unknowns without shipping new code? If yes, you have observability. If no, you have monitoring.

The "three pillars" (metrics/logs/traces) is a vendor marketing construct. The real three pillars are:
1. **High cardinality** — UUID-level uniqueness in fields; metrics cannot handle this
2. **High dimensionality** — hundreds of fields per event
3. **Explorability** — ad hoc questions without predicting them

### 2. Monitoring vs. Observability (Ch 1, 2, 9) — the split

| | Monitoring | Observability |
|---|---|---|
| Failure type | Known-unknowns | Unknown-unknowns |
| Best for | Systems/infrastructure | Software/application code |
| Alerting | Cause-based thresholds | Symptom-based SLOs |
| Debugging | Pattern-match to known issues | Core analysis loop from first principles |
| Best debugger | Most experienced (seniority) | Most curious (methodology) |

Use monitoring for infrastructure that changes monthly with predictable failure modes. Use observability for code that changes daily with novel failure modes. The split is by operational responsibility, not by where infrastructure lives.

### 3. Arbitrarily Wide Structured Events (Ch 5) — the data type

Initialize an empty map at request entry. Throughout execution, append everything: user IDs, parameters, durations, remote call results, errors, host metadata, business values. Emit the full map as a structured key-value record on exit. No field limits. No schema predeclared.

**Why not metrics**: Pre-aggregation at write time destroys cardinality. You can never ask new questions of pre-aggregated data. The arms race of adding more metrics is both expensive and ultimately insufficient.

**Mature instrumentation**: 300–400 dimensions per event.

### 4. The Core Analysis Loop (Ch 8) — the debugging workflow

1. **Context**: What did the alert or customer tell you?
2. **Verify**: Is a notable change in performance actually happening? Visualize.
3. **Search dimensions**: (a) Sample rows for outliers; (b) GROUP BY common fields (status_code, build_id, AZ); (c) Filter to narrow scope.
4. **Decide**: Root cause found? Done. If not, filter to isolate the anomaly, return to step 3.

Automate the brute-force portion: compute dimension values inside anomaly vs. outside (baseline), sort by percent difference. Humans apply context; machines crunch data.

**Requirement**: Only possible with arbitrarily wide structured events and a query tool returning results in seconds.

### 5. SLO-Based Alerting (Ch 12, 13) — reliable alerts

**Two-criteria test** — delete any alert that fails either:
1. Reliable indicator that **user experience is degraded**
2. **Actionable** — systematic way to investigate and fix

**SLO structure**: SLI (good/bad per request) → SLO target (e.g., 99.9% good over 30 days) → error budget (0.1% of requests allowed to fail) → burn alerts.

**Predictive burn alert rules**:
- Use 30-day **sliding window** (not calendar)
- Lookahead:baseline ratio: **4:1 max** (6h baseline → 24h lookahead; 1h baseline → 4h lookahead)
- Configure both short AND long lookahead windows
- Use **event-based SLIs** (per request) not time-based (per minute window)

**Decoupling "what" from "why"**: SLO alerts tell you user experience is degraded. Observability (core analysis loop) tells you why.

### 6. OpenTelemetry Instrumentation (Ch 7) — the standard

OTel = merger of OpenTracing + OpenCensus (2019, CNCF). Instrument once, send anywhere.

Strategy:
1. Auto-instrument HTTP/gRPC frameworks (immediate service topology skeleton)
2. Add custom spans for expensive paths
3. Add business attributes: customer ID, feature flags, cart value, build ID
4. Export via **OTLP gRPC** (not vendor-specific exporters)

For every PR: "How will I know if this change is working as intended?" Custom instrumentation is the answer.

Prefer OTel over vendor agents: vendor agents create lock-in that requires full re-instrumentation to switch backends.

### 7. Observability-Driven Development (Ch 11) — the practice

ODD extends TDD: TDD validates code against isolated specification; ODD validates behavior in messy production reality.

Key rules:
- Bundle instrumentation with every feature, the way you bundle tests
- Route alerts to the engineer who just merged for the first 30–60 minutes — fastest feedback loop
- Observability operates at system level (find WHERE the problem is); debuggers operate at function level (find WHAT in the code)
- Production is an interactive playground, not a glass castle

The key metric for team health: **time from code written to code in production**.

### 8. Sampling Strategy (Ch 17) — cost control

Sampling preserves full cardinality on selected events (unlike aggregation, which destroys it). Always record `sampleRate` in each sampled event.

- **Consistent sampling**: Use upstream Sampling-ID across all services so full traces are captured or dropped atomically.
- **Head-based**: Decision at trace start, propagated downstream. Use when criteria known up front.
- **Tail-based (buffered)**: Decision after trace completes. Use for outcome-based criteria (latency, error status). Requires collector-side buffering.
- **Per-key target rate**: Different rates for normal vs. outlier events (1/1000 success, 1/1 errors). Use dynsampler-go.

### 9. Data Storage Requirements (Ch 16) — what to build or evaluate

For observability workloads, TSDBs cause cardinality explosion. Standard NoSQL databases require pre-indexing that defeats arbitrary queries.

Requirements: (1) Query results in seconds; (2) Any field queryable ad hoc; (3) Data queryable within seconds of ingestion; (4) All dimensions equally fast (only time is privileged); (5) Fault-tolerant and durable.

Solution: Hybrid columnar store with time-partitioned segments. Each segment stores columns as independent files; time metadata enables query scope narrowing without sorting all data. Parallelize with serverless compute over cloud object storage (S3).

Viable alternatives: Google BigQuery, ClickHouse, Apache Druid.

### 10. Build vs. Buy (Ch 15) — the decision

**Default recommendation**: Buy + build. Purchase a commercial backend with strong APIs. Create an internal observability team to write libraries, standardize naming, manage vendor relationships — NOT to rebuild the core tool.

"Free" ELK stacks: one real-world example → $80K/month infrastructure + 3 engineers × $300K = >$2M/year. Commercial option: <$1M/year.

OTel prevents lock-in: instrumentation is reusable across any backend.

---

## Chapter Index

| # | Title | Part | Key Frameworks |
|---|-------|------|----------------|
| [ch01](chapters/ch01-what-is-observability.md) | What Is Observability? | I | Observability definition, three real pillars, cardinality, dimensionality |
| [ch02](chapters/ch02-debugging-practices.md) | How Debugging Practices Differ | I | Monitoring vs. observability debugging, tool-hopping, institutional knowledge |
| [ch03](chapters/ch03-lessons-from-parse.md) | Lessons from Scaling Without Observability | I | Parse case study, hero culture cycle, retrospective trap |
| [ch04](chapters/ch04-devops-sre-cloud-native.md) | Observability & DevOps/SRE/Cloud Native | I | Sociotechnical coupling, SLO/chaos/feature flag prerequisites |
| [ch05](chapters/ch05-structured-events.md) | Structured Events Are the Building Blocks | II | Arbitrarily wide structured events, metric limitations, structured log design |
| [ch06](chapters/ch06-stitching-events-into-traces.md) | Stitching Events into Traces | II | Trace assembly (5 required fields), context propagation, B3 headers |
| [ch07](chapters/ch07-instrumentation-opentelemetry.md) | Instrumentation with OpenTelemetry | II | OTel components, auto + custom instrumentation, OTLP exporter code |
| [ch08](chapters/ch08-analyzing-events.md) | Analyzing Events to Achieve Observability | II | Core analysis loop, BubbleUp, AIOps limitations |
| [ch09](chapters/ch09-observability-and-monitoring-together.md) | How Observability and Monitoring Come Together | II | Systems vs. software split, coexistence patterns |
| [ch10](chapters/ch10-applying-observability-in-teams.md) | Applying Observability Practices in Your Team | III | Pain-first adoption, buy before build, iterative instrumentation |
| [ch11](chapters/ch11-observability-driven-development.md) | Observability-Driven Development | III | ODD vs. TDD, glass castle, feedback loop tightening |
| [ch12](chapters/ch12-slos-for-reliability.md) | Using SLOs for Reliability | III | Two-criteria alert test, SLO/SLI/error budget, event-based SLIs |
| [ch13](chapters/ch13-slo-burn-alerts.md) | Acting on and Debugging SLO-Based Alerts | III | Predictive burn alerts, lookahead:baseline 4:1, proportional extrapolation, Go code |
| [ch14](chapters/ch14-software-supply-chain.md) | Observability and the Software Supply Chain | III | CI/CD tracing, Slack case study, flaky test reduction |
| [ch15](chapters/ch15-build-vs-buy.md) | Build Versus Buy and ROI | IV | TCO analysis, buy-and-build model, OTel distros |
| [ch16](chapters/ch16-efficient-data-storage.md) | Efficient Data Storage | IV | Retriever architecture, columnar storage, cardinality explosion, query workload |
| [ch17](chapters/ch17-sampling.md) | Cheap and Accurate Enough: Sampling | IV | Sampling taxonomy, head/tail/buffered, consistent sampling, Go code |
| [ch18](chapters/ch18-telemetry-pipelines.md) | Telemetry Management with Pipelines | IV | Pipeline anatomy, Slack Murron, SpanEvent, open source alternatives |
| [ch19](chapters/ch19-business-case.md) | The Business Case for Observability | V | ROI four categories, break-point symptoms, blameless culture prerequisite |
| [ch20](chapters/ch20-stakeholders-and-allies.md) | Observability's Stakeholders and Allies | V | Non-engineering use cases, observability vs. BI tools comparison |
| [ch21](chapters/ch21-maturity-model.md) | An Observability Maturity Model | V | OMM 5 capabilities, survey data, Wardley mapping guidance |
| [ch22](chapters/ch22-where-to-go.md) | Where to Go from Here | V | Final definition, predictions for OTel/frontend, recommended resources |

---

## Topic Index

- **AIOps** → ch08
- **Alert fatigue** → ch12, ch19
- **Alerting (SLO-based)** → ch12, ch13
- **Arbitrarily wide structured events** → ch05, ch08, ch16
- **B3 / W3C trace headers** → ch06, ch07
- **BubbleUp (automated core analysis)** → ch08
- **Build vs. buy** → ch10, ch15, ch19
- **Cardinality (high)** → ch01, ch05, ch16
- **CI/CD pipeline observability** → ch14
- **Core analysis loop** → ch08, ch12, ch19
- **Context propagation** → ch06, ch07
- **Columnar data store** → ch16
- **Debugging from first principles** → ch08
- **Dimensionality (high)** → ch01, ch05
- **DevOps / SRE / Cloud native** → ch04
- **Error budget** → ch12, ch13
- **Explorability** → ch01
- **Feature flags** → ch04, ch11
- **Glass castle mentality** → ch03, ch11
- **Head-based sampling** → ch17
- **Hero culture** → ch03
- **Instrumentation** → ch07, ch10, ch11
- **Known-unknowns / unknown-unknowns** → ch01, ch09, ch12
- **Maturity model (OMM)** → ch21
- **Metrics limitations** → ch01, ch05, ch09, ch16
- **Monitoring coexistence** → ch09
- **Normalization of deviance** → ch12
- **Observability-driven development (ODD)** → ch11
- **OpenTelemetry (OTel)** → ch07, ch10, ch15, ch19
- **OTLP** → ch07
- **Parse case study** → ch03
- **Predictive burn alerts** → ch13
- **Progressive delivery** → ch03, ch11
- **ROI / business case** → ch15, ch19
- **Sampling** → ch17, ch18
- **Scuba (Facebook)** → ch03
- **SLI / SLO / error budget** → ch12, ch13
- **Sliding window** → ch13
- **Stakeholders / allies** → ch20
- **Structured events** → ch05, ch06
- **Systems vs. software** → ch09
- **Tail-based sampling** → ch17, ch18
- **TCO analysis** → ch15
- **Telemetry pipelines** → ch18
- **TSDB inadequacy** → ch05, ch16
- **Traces / distributed tracing** → ch06, ch07

## Supporting Files

- [glossary.md](glossary.md) — ~40 key terms with definitions
- [patterns.md](patterns.md) — all major techniques with When/How/Trade-offs
- [cheatsheet.md](cheatsheet.md) — quick reference tables and decision guides

---

## Scope & Limits

This skill covers the book content only. The book was written in early 2022; OTel, Honeycomb, and the broader observability ecosystem have continued to evolve. The core frameworks (structured events, core analysis loop, SLO-based alerting, sampling strategy) remain authoritative. For current OTel API versions or specific vendor features, check current documentation.

For hands-on implementation, combine with project-specific tools. The observability engineering principles in this book are vendor-neutral and apply regardless of which backend you use.
