# Chapter 10: AI Engineering Architecture and User Feedback

## Core Idea
Build AI applications incrementally starting from the simplest architecture, adding components only as needs arise; user feedback is a proprietary data asset and must be deliberately designed into the system.

## Frameworks Introduced

- **5-Step Progressive Architecture**:
  1. Enhance context (RAG, tool use)
  2. Put in guardrails (input PII masking, output safety filtering)
  3. Add model router and gateway (multi-model pipelines, security)
  4. Reduce latency with caches (exact, semantic, KV, prompt caches)
  5. Add agent patterns (loops, write actions, conditional branching)

- **Guardrail Design**:
  - Input guardrails: PII masking before sending to external APIs, prompt injection detection.
  - Output guardrails: catch malformatted responses, hallucinations, toxic content; retry logic; human fallback.
  - Trade-off: reliability vs. latency (guardrails add latency; some teams skip them for speed).

- **Observability Three Metrics** (from DevOps):
  - MTTD: mean time to detection
  - MTTR: mean time to response
  - CFR: change failure rate (% of deployments causing failures)

- **User Feedback Taxonomy**:
  - Explicit feedback: thumbs up/down, ratings, correction submissions.
  - Implicit feedback: stop generation, regenerate, copy to clipboard, conversion events.
  - Conversation signals: session length, turns per session, message length trends.

- **Data Flywheel**: Production usage → user feedback → annotation → model improvement → better product → more usage.

## Key Concepts

- **Model gateway**: Abstraction layer for multiple model APIs; provides routing, auth, rate limiting, cost tracking. Reduces vendor lock-in.
- **Model router**: Directs queries to the most appropriate model based on task type, cost, latency, or performance requirements.
- **Exact caching**: Cache and return previously computed outputs for identical queries.
- **Semantic caching**: Cache outputs for semantically similar queries (requires embedding + vector search); higher hit rate, higher complexity risk.
- **Orchestrator**: Manages component chaining in AI pipelines; LangChain, LlamaIndex, Haystack.
- **Input guardrail**: Protects system and external APIs by detecting and masking sensitive data or rejecting malicious prompts.
- **Output guardrail**: Catches model failures (empty response, wrong format, hallucinations, toxic content); triggers retry or human fallback.
- **PII reverse dictionary**: Maps masked placeholders (e.g., [PHONE NUMBER]) to original values for unmasking after model call.
- **Retry policy**: On output failure, retry up to N times or send parallel requests to pick the best response.
- **Stream completion mode**: Tokens streamed to user as generated (lower perceived latency) but makes output guardrails harder.
- **Drift detection**: Monitoring for system prompt changes, user behavior changes, and silent model API updates.
- **Traces**: End-to-end record of a request's path through all system components; shows time and cost per step.
- **Metrics vs. logs vs. traces**: Metrics = aggregated numbers; logs = append-only event records; traces = linked event timelines.
- **AI pipeline orchestration**: Components definition + chaining; ensure data flows between components in correct format.

## Mental Models

- Start simple: the simplest architecture that works is the best architecture. Add complexity only when a specific problem demands it.
- Caching has a data leak risk — user-specific cached responses can be served to different users.
- Semantic caching is tempting but brittle: requires good embeddings, reliable vector search, and threshold tuning. Validate carefully.
- Design for observability from the start; retrofitting it is painful.

## Reference Tables

**Architecture progression components:**

| Step | Component | What it solves |
|---|---|---|
| 1 | RAG / tool use | Model lacks task-specific knowledge |
| 2 | Input/output guardrails | Privacy risks, safety failures |
| 3 | Model router/gateway | Multi-model pipelines, vendor lock-in |
| 4 | Caching | Latency and cost for repeated queries |
| 5 | Agents / write actions | Complex multi-step automation |

**Caching types comparison:**

| | Exact cache | Semantic cache |
|---|---|---|
| When to use | Identical repeated queries | Similar but not identical queries |
| Reliability | High | Moderate (depends on embedding quality) |
| Complexity | Low | High (needs vector DB + threshold tuning) |
| Data leak risk | Lower | Higher (semantically similar ≠ same user) |

**User feedback signal types:**

| Signal type | Examples | Value |
|---|---|---|
| Explicit positive | Thumbs up, copy, bookmark | High signal, low volume |
| Explicit negative | Thumbs down, correction, report | High signal, low volume |
| Implicit positive | Long session, many turns, subscription | Medium signal, high volume |
| Implicit negative | Stop generation, short session, churn | Medium signal, high volume |

## Anti-patterns

- **Jumping to complex architecture on day 1**: Start with the simplest model → query → response loop; add components as specific problems emerge.
- **No observability**: MTTD and MTTR are undefined; failures go undetected.
- **Semantic caching without validation**: Incorrect cached results are served without detection; validate hit rate and correctness.
- **Enabling write actions without validation**: Autonomous write actions require strong guardrails, testing, and human-in-the-loop checkpoints.
- **Starting with orchestration tools**: Tools abstract away details, making debugging harder; understand the pipeline first, then add orchestrators.
- **Implicit-only feedback system**: Implicit signals are weak; design at least one explicit feedback mechanism.

## Key Takeaways

1. Progressive architecture: start simple, add components only when a specific problem demands it.
2. Guardrails are mandatory for production but have latency costs; design them thoughtfully.
3. Model gateways reduce vendor lock-in and enable A/B testing across model providers.
4. User feedback is a competitive moat; design explicit + implicit feedback collection into the product.
5. Observability must be built in from the start: metrics, logs, and traces are all needed.
6. The data flywheel connects user feedback to model improvement; design for it explicitly.

## Connects To

- **Ch 6**: RAG components appear as Step 1 in the architecture.
- **Ch 5**: Prompt injection defense is Step 2 (guardrails).
- **Ch 9**: KV cache and prompt caching are Step 4 in the architecture.
- **Ch 3**: Evaluation metrics translate to monitoring metrics in production.
- **Ch 8**: User feedback creates the data flywheel that feeds back into dataset engineering.
