---
name: huyen-ai-engineering
description: "Knowledge base from \"AI Engineering: Building Applications with Foundation Models\" by Chip Huyen. Use when applying Huyen's frameworks for foundation model adaptation, evaluation methodology, RAG architecture, prompt engineering, finetuning decisions, inference optimization, dataset engineering, and production AI system design."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, framework name, or chapter number — e.g. 'RAG', 'LoRA', 'evaluation', 'ch06']"
---

# AI Engineering: Building Applications with Foundation Models
**Author**: Chip Huyen  |  **Pages**: ~450  |  **Chapters**: 10  |  **Generated**: 2026-06-02

## How to Use This Skill

- **Without arguments** — load the core frameworks and mental models below
- **With a topic** — ask about `RAG`, `finetuning`, `sampling`, `evaluation`, `agents`, etc.; I find the relevant chapter
- **With chapter** — ask for `ch06` or `ch09`; I load that chapter file
- **Browse** — ask "what chapters do you have?" to see the full index

For details beyond the core frameworks below, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md` files in this skill.

---

## Core Frameworks & Mental Models

### 1. The Adaptation Decision Hierarchy (Ch 5–7)

Try in this order; stop when performance is sufficient:
1. **Zero-shot prompting** — free, instant
2. **Few-shot prompting** — add 1–50 examples in context
3. **Term-based RAG** (BM25) — connect to external knowledge; start here, not vector DBs
4a. **Embedding-based / hybrid RAG** — when BM25 retrieval quality is insufficient
4b. **Finetuning (LoRA)** — when model has behavioral issues (format, style, safety)
5. **RAG + Finetuning** — for maximum quality on both knowledge and behavior

**Core heuristic**: "RAG is for facts, finetuning is for form."
Finetuning cannot reliably add new knowledge; it changes how the model behaves. RAG adds knowledge; it does not change behavior.

### 2. Evaluation-Driven Development (Ch 3–4)

Define evaluation criteria **before** building. Four criteria buckets:
1. Domain-specific capability (can the model do the task at all?)
2. Generation capability (coherent, faithful, on-tone output)
3. Instruction-following capability (correct format, length constraints)
4. Cost and latency (tokens consumed, response time)

**Evaluation method picker**:
- Functional correctness (pass@k) for code/SQL — automatable gold standard
- Exact match for short, unambiguous answers
- AI as a judge (pairwise) for open-ended text — 85% human agreement on MT-Bench
- Build private benchmarks from your production data; public benchmarks are for initial filtering only

**Anti-pattern**: Eyeballing results. A model that can't be measured can't be improved.

### 3. Sampling Fundamentals (Ch 2)

Sampling is the most underrated lever. Before changing models or finetuning, tune sampling parameters:

- **Temperature**: T=0 = deterministic (classification, structured output); T=0.7 = creative tasks; T>1 = very random
- **Top-p (nucleus)**: 0.9–0.95 for most tasks; dynamically adjusts candidate set size
- **Top-k**: fixed candidate set; less adaptive than top-p but reduces computation
- **Best-of-N**: generate N outputs, score with reward model, pick best — inference-time quality boost without RL

Temperature divides logits before softmax. T=0 means always pick the max logit (greedy).

### 4. RAG Architecture (Ch 6)

**RAG = feature engineering for foundation models.** Key components:
- **Retriever**: indexes documents, queries for relevant chunks per request
- **Generator**: augmented with retrieved context

Start simple:
1. BM25 term-based retrieval (fast, cheap, strong baseline)
2. Add embedding-based reranking only when BM25 is insufficient
3. Use hybrid search + RRF to combine both: `Score(D) = Σ 1/(k + rank_i(D))`

**Retrieval optimization order**: chunking strategy → reranking → query rewriting → contextual retrieval

**Long context vs. RAG**: Long context doesn't eliminate RAG. RAG filters signal; raw context adds noise. Start RAG at ~200K tokens even for Claude models.

### 5. Memory Math for Finetuning (Ch 7)

```
Inference memory = N × bytes/param × 1.2
Full finetuning memory ≈ 4× inference memory (gradients + Adam states)
LoRA: trainable params = 2 × n × r per layer (r = 16–64 typical)
```

**FP16 (2B/param)**: 7B model = 14 GB weights; full finetuning = ~58 GB; LoRA = ~16 GB

**LoRA formula**: `W' = W + (α/r) × A@B`
- Only A and B are trainable; W is frozen; merge at inference for zero latency overhead
- With r=3% of full params, achieves within 0.4% of full finetuning quality (GLUE)

### 6. Inference Optimization (Ch 9)

**Two bottlenecks**:
- Prefill: processes all input tokens in parallel → **compute-bound**
- Decode: generates one token at a time → **memory bandwidth-bound**

Decode speed = main bottleneck. Quantization (fewer bytes/param) directly reduces bandwidth usage.

**Key techniques**:
- KV cache: store computed attention vectors; reuse in decode
- Prompt caching: reuse KV cache for repeated system prompts (50–90% cost reduction)
- Continuous batching: insert new requests dynamically into ongoing batches
- Speculative decoding: small draft model proposes, large model verifies in parallel

**MBU formula**: `(params × bytes × tokens/s) / theoretical_bandwidth` — measures bandwidth efficiency

### 7. Progressive Production Architecture (Ch 10)

Start minimal; add layers as specific problems emerge:
1. query → model → response (baseline)
2. + RAG / tools (knowledge gaps)
3. + guardrails (PII masking, safety filters)
4. + model gateway (multi-model, vendor lock-in)
5. + caching (latency, cost)
6. + agent loops and write actions (automation)

User feedback is a competitive moat. Design explicit (thumbs, corrections) + implicit (session length, stop generation) feedback collection. Build the **data flywheel**: usage → feedback → annotation → improved model.

### 8. Post-Training Pipeline (Ch 2)

Pre-training (self-supervised, 98% of compute) → SFT (demonstration data, behavior alignment) → Preference finetuning (RLHF or DPO, human preference alignment)

- Pre-training = knowledge acquisition ("reading")
- SFT = learning to have conversations ("conversational training")
- Preference finetuning = alignment with human values ("social norms")

DPO is simpler than RLHF (used by Llama 3); RLHF is more flexible (used by GPT-3.5, Llama 2).

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-intro-building-ai-apps.md) | Introduction to Building AI Applications | 3-layer AI stack, adaptation categories, AI vs. ML engineering |
| [ch02](chapters/ch02-understanding-foundation-models.md) | Understanding Foundation Models | Chinchilla scaling law, sampling strategies, post-training pipeline, memory math |
| [ch03](chapters/ch03-evaluation-methodology.md) | Evaluation Methodology | AI as a judge, functional correctness, perplexity/cross-entropy, embeddings |
| [ch04](chapters/ch04-evaluate-ai-systems.md) | Evaluate AI Systems | Evaluation-driven development, 4 criteria buckets, model selection, private benchmarks |
| [ch05](chapters/ch05-prompt-engineering.md) | Prompt Engineering | CoT, in-context learning, prompt decomposition, defensive prompting, chat templates |
| [ch06](chapters/ch06-rag-and-agents.md) | RAG and Agents | RAG architecture, hybrid search, RRF, chunking, agents, tool inventory, planning |
| [ch07](chapters/ch07-finetuning.md) | Finetuning | LoRA, PEFT, memory bottlenecks, RAG vs. finetuning decision, numerical formats |
| [ch08](chapters/ch08-dataset-engineering.md) | Dataset Engineering | 3 golden goals, data quality 6 criteria, LIMA principle, data flywheel |
| [ch09](chapters/ch09-inference-optimization.md) | Inference Optimization | Prefill/decode split, KV cache, quantization, continuous batching, MFU/MBU |
| [ch10](chapters/ch10-ai-engineering-architecture.md) | AI Engineering Architecture & User Feedback | 5-step architecture, guardrails, model gateway, caching, observability, user feedback |

---

## Topic Index

- **Agents** → ch06, ch10
- **AI as a judge** → ch03, ch04
- **Architecture (production)** → ch10
- **Best-of-N** → ch02
- **BM25 / term-based retrieval** → ch06
- **Caching (KV, prompt, semantic, exact)** → ch09, ch10
- **Chain-of-thought (CoT)** → ch05, ch08
- **Chinchilla scaling law** → ch02
- **Chunking** → ch06
- **Continuous batching** → ch09
- **Cross entropy / perplexity** → ch03
- **Data flywheel** → ch08, ch10
- **Dataset engineering** → ch08
- **DPO** → ch02
- **Embeddings** → ch03, ch06
- **Evaluation criteria** → ch04
- **Evaluation methods** → ch03, ch04
- **Evaluation-driven development** → ch04
- **Finetuning decision (vs. RAG)** → ch07
- **Finetuning techniques** → ch07
- **Foundation models** → ch01, ch02
- **Functional correctness** → ch03, ch04
- **Goodput** → ch09
- **Guardrails** → ch10
- **Hallucination** → ch01, ch03, ch06
- **Hybrid search** → ch06
- **In-context learning (ICL)** → ch05
- **Inference optimization** → ch09
- **KV cache** → ch09, ch10
- **LIMA principle** → ch08
- **LoRA** → ch07
- **Memory math** → ch07, ch09
- **MFU / MBU** → ch09
- **Model drift** → ch04, ch10
- **Model gateway** → ch10
- **Model merging** → ch07
- **Model selection** → ch04
- **MoE (Mixture-of-Experts)** → ch02
- **Observability** → ch10
- **Orchestration** → ch10
- **PEFT** → ch07
- **Post-training (SFT + RLHF/DPO)** → ch02
- **Prefill / decode** → ch02, ch09
- **Prompt attacks (injection, jailbreaking)** → ch05
- **Prompt caching** → ch09
- **Prompt decomposition** → ch05
- **Prompt engineering** → ch05
- **Prompt versioning** → ch05
- **Quantization** → ch07, ch09
- **Query rewriting** → ch06
- **RAG architecture** → ch06
- **RAG evaluation** → ch03, ch06
- **RAG optimization** → ch06
- **Reciprocal Rank Fusion (RRF)** → ch06
- **Reranking** → ch06
- **RLHF** → ch02
- **Sampling (temperature, top-k, top-p)** → ch02
- **Scaling law** → ch02
- **Self-supervision** → ch01, ch02
- **Semantic caching** → ch10
- **SFT (Supervised Finetuning)** → ch02, ch07
- **Soft prompts** → ch07
- **Speculative decoding** → ch09
- **Synthetic data** → ch08
- **Token** → ch01
- **TTFT / TPOT** → ch09
- **User feedback** → ch10

## Supporting Files

- [glossary.md](glossary.md) — all key terms with definitions (~90 terms alphabetical)
- [patterns.md](patterns.md) — all named techniques and design patterns with When/How/Trade-offs
- [cheatsheet.md](cheatsheet.md) — quick reference tables, decision guides, formulas

---

## Scope & Limits

This skill covers the book content only (First Edition, December 2024). It does not include:
- Model releases or API changes after the book's writing (late 2024)
- Hands-on implementation guides for specific frameworks (LangChain, LlamaIndex, vLLM)
- Current pricing for model APIs (check provider docs)

The author's core frameworks — adaptation hierarchy, evaluation-driven development, RAG vs. finetuning — are technology-agnostic and should remain relevant beyond specific tools. For hands-on implementation, combine this skill with current documentation and project-specific tools.
