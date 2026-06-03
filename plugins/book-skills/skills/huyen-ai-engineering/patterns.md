# Patterns & Techniques — AI Engineering (Chip Huyen)

## Chinchilla Scaling Law
**When to use**: Before training or finetuning a model — to determine compute-optimal model size and dataset size for a given budget.
**How**: Number of training tokens ≈ 20× parameter count. Scale model size and data size equally (double both together). Compute budget = training FLOPs; use this to derive both.
**Trade-offs**: Assumes dense models and human-generated data. MoE and synthetic data require adjusted calculations.

---

## Best-of-N Sampling
**When to use**: You want RLHF-quality outputs without full RL training; inference-time quality boost.
**How**: Generate N candidate outputs for each query → score all with a reward model → return the highest-scoring. Stitch Fix and Grab use this without PPO.
**Trade-offs**: N× the inference cost; requires a reward model; not suitable for latency-sensitive applications.

---

## Evaluation-Driven Development
**When to use**: Starting any new AI application.
**How**: Define success criteria (domain capability, generation quality, instruction following, cost/latency) before writing a line of code. Build private benchmark from production data. Evaluate before deploying.
**Trade-offs**: Upfront investment in evaluation infrastructure; reduces risk of building unmeasurable applications.

---

## AI as a Judge (Pairwise)
**When to use**: Open-ended generation tasks where reference data doesn't exist or is incomplete; generating preference data for finetuning.
**How**: For each query, generate two responses → prompt LLM judge to pick the better one → use pairwise comparisons to rank models or generate training data.
**Trade-offs**: Positional bias (judge may prefer response A over B regardless of quality); use LLM judge + human spot-check calibration.

---

## Progressive Adaptation Framework
**When to use**: Adapting a foundation model to a specific application.
**How**:
1. Zero-shot prompting
2. Few-shot prompting (1–50 examples in context)
3. Term-based RAG (BM25)
4a. Advanced RAG (embedding-based) — if knowledge gaps persist
4b. Finetuning — if behavioral issues persist
5. RAG + Finetuning combined
**Trade-offs**: Each step adds cost, complexity, and infrastructure; do not skip ahead.

---

## Prompt Decomposition
**When to use**: Complex tasks requiring multiple steps; tasks where intermediate outputs need monitoring.
**How**: Break the main task into subtasks → assign each subtask its own prompt → chain outputs as inputs to the next prompt. Use weaker/cheaper models for simple subtasks (e.g., intent classification).
**Trade-offs**: Increased latency for sequential chains; parallelizable steps mitigate this.

---

## Chain-of-Thought (CoT) Prompting
**When to use**: Multi-step reasoning, math, logical deduction, reducing hallucinations.
**How**: Add "think step by step" (zero-shot) or specify explicit reasoning steps, or include an example with reasoning (one-shot CoT).
**Trade-offs**: Longer outputs = higher cost and latency; for time-sensitive tasks, consider whether the quality gain justifies the cost.

---

## RAG with Hybrid Search
**When to use**: Production retrieval systems requiring both keyword precision and semantic understanding.
**How**:
1. BM25 term-based retrieval fetches initial candidates (fast, cheap)
2. Embedding-based reranking selects the best candidates
3. Reciprocal Rank Fusion (RRF) can combine rankings from both: `Score(D) = Σ 1/(k + rank_i(D))` with k=60
**Trade-offs**: Adds vector DB cost and embedding latency; only necessary when BM25 alone is insufficient.

---

## Chunking Strategy for RAG
**When to use**: Indexing documents for retrieval.
**How**: Options in order of sophistication:
1. Fixed-size chunks (e.g., 512 tokens with 20-token overlap)
2. Recursive splitting (section → paragraph → sentence)
3. Semantic chunking (split at natural semantic boundaries)
4. Document-specific (Q&A by pair, code by function)
Overlap: always include 10–20% overlap between consecutive chunks.
**Trade-offs**: Smaller chunks = more diversity but more computation; larger chunks = more context but more noise.

---

## LoRA Finetuning
**When to use**: Memory-constrained finetuning of large models; adding task-specific behavior without changing base model weights.
**How**: For weight matrix W (n×m), choose rank r << min(n,m). Construct A (n×r) and B (r×m). During finetuning, update only A and B. New weight: `W' = W + (α/r) × A@B`. Merge A@B into W after finetuning for zero inference overhead.
**Trade-offs**: Lower r = less memory, potentially lower quality; r=16 to r=64 is typical range. Use QLoRA (quantized base model + LoRA) for even lower memory.

---

## Continuous Batching
**When to use**: High-throughput inference servers with variable-length requests.
**How**: Instead of waiting for all requests in a static batch to finish, dynamically insert new requests into the batch as slots open up. Requires an inference framework that supports this (vLLM, TensorRT-LLM).
**Trade-offs**: More complex scheduling; trades per-request latency for overall throughput.

---

## Speculative Decoding
**When to use**: Latency-sensitive applications where decode speed is the bottleneck.
**How**: Small draft model generates N candidate tokens → large target model verifies all N in a single forward pass → accept tokens up to the first rejection.
**Trade-offs**: Only effective when draft model has high acceptance rate (depends on how similar draft and target model distributions are); adds draft model hosting cost.

---

## 5-Step Progressive Architecture
**When to use**: Building any production AI application.
**How**:
1. Start: query → model → response (no extras)
2. Add: RAG / tool use for context
3. Add: guardrails (PII masking, output safety)
4. Add: model router/gateway
5. Add: caching (exact, semantic, prompt)
6. Add: agent loops and write actions
**Trade-offs**: Each layer adds latency and complexity; add only when a specific problem justifies it.

---

## Data Flywheel
**When to use**: Building AI products with long-term improvement goals.
**How**: Deploy product → collect user feedback (explicit: thumbs, corrections; implicit: session length, stop generation) → annotate/curate feedback into training data → retrain or finetune model → improved product → more usage.
**Trade-offs**: Requires upfront investment in feedback collection infrastructure and annotation pipeline; compounding returns over time.

---

## PII Masking with Reverse Dictionary
**When to use**: Sending user queries containing private data to external model APIs.
**How**: Detect PII in input → replace with placeholder (e.g., `[PHONE_NUMBER_1]`) → send masked query to API → unmask placeholders in response using reverse map before returning to user.
**Trade-offs**: Risk of incomplete PII detection; model may not handle placeholder tokens well; requires careful regex/ML detection.

---

## Prompt Catalog with Versioning
**When to use**: Multiple applications sharing prompts; production prompt management.
**How**: Store prompts in a separate file (`prompts.py`) or catalog with metadata (model, date, application, creator, sampling params). Version with git or explicit version numbers. Allow different applications to pin to specific prompt versions.
**Trade-offs**: Coordination overhead for shared prompts; enables auditability and rollback.
