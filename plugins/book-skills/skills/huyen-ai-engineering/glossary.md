# Glossary — AI Engineering (Chip Huyen)

**Activation recomputation** — During backpropagation, recompute activations instead of storing them to save memory (at cost of compute). Also called gradient checkpointing. (Ch 7)

**Adapter-based methods** — PEFT techniques that insert trainable modules into a frozen base model (LoRA, IA3, original Houlsby adapters). (Ch 7)

**AI as a judge** — Using an LLM to evaluate another LLM's outputs; three modes: pointwise scoring, reference comparison, pairwise preference. (Ch 3)

**Autoregressive LM** — Language model that predicts the next token using only preceding tokens; dominant architecture for text generation. (Ch 1, 2)

**Best-of-N strategy** — Generate N outputs, score each with a reward model, return the highest-scored; alternative to RL-based preference finetuning. (Ch 2)

**BM25** — Classical term-based retrieval algorithm (a form of TF-IDF); strong baseline for RAG; preferred starting point before embedding-based retrieval. (Ch 6)

**BPC/BPB** — Bits-per-character / bits-per-byte; normalized cross entropy for cross-model comparison. (Ch 3)

**Chain-of-thought (CoT)** — Prompt technique asking model to reason step-by-step; improves multi-step reasoning and reduces hallucinations. (Ch 5)

**Chinchilla scaling law** — ~20 training tokens per parameter is compute-optimal; scale model size and dataset size equally. (Ch 2)

**Chunking** — Splitting documents into pieces for retrieval; affects RAG quality significantly. (Ch 6)

**Continued pre-training** — Self-supervised finetuning on domain-specific data before SFT; cheap domain adaptation. (Ch 7)

**Continuous batching** — Dynamically insert new requests into ongoing batches to improve GPU utilization during inference. (Ch 9)

**Cross entropy** — Loss function measuring how well a model predicts the next token; lower = better. H(P,Q) = H(P) + KL(P||Q). (Ch 3)

**Data flywheel** — Virtuous cycle: usage → feedback → annotation → improved model → more usage. (Ch 8, 10)

**Decode** — Sequential phase of autoregressive inference that generates one token at a time; memory bandwidth-bound. (Ch 2, 9)

**Demonstration data** — (instruction, response) pairs showing model desired behavior; used for SFT. (Ch 2, 8)

**DPO (Direct Preference Optimization)** — Simpler alternative to RLHF for preference finetuning; used by Llama 3. (Ch 2)

**Embedding** — Numerical vector representation capturing semantic meaning of data. (Ch 3)

**Evaluation-driven development** — Define evaluation criteria before building, analogous to test-driven development. (Ch 4)

**Exact caching** — Cache and return outputs only for identical queries. (Ch 10)

**Foundation models** — Large-scale general-purpose AI models (LLMs and LMMs) trained via self-supervision at massive scale. (Ch 1)

**Full finetuning** — Update all model parameters during training; maximum quality but maximum memory cost. (Ch 7)

**Functional correctness** — Evaluation by executing code against unit tests; gold standard for code generation. (Ch 3)

**Goodput** — Requests per second satisfying the latency SLO; more meaningful than raw throughput. (Ch 9)

**Gradient checkpointing** — See activation recomputation. (Ch 7)

**Greedy sampling** — Always pick the highest-probability next token; deterministic but repetitive. (Ch 2)

**Guardrails** — Input/output filters that protect system and users; PII masking, safety filtering, format validation. (Ch 10)

**Hallucination** — Model generating plausible-sounding but factually incorrect information. (Ch 1, 3, 6)

**Hybrid search** — Combining term-based retrieval and embedding-based retrieval; first fetch candidates with BM25, then rerank with vectors. (Ch 6)

**In-context learning (ICL)** — Model learns desired behavior from examples in the prompt without weight updates. (Ch 5)

**Inference server** — Component that hosts and runs models; executes forward passes for incoming requests. (Ch 9)

**KV cache** — Cached key-value attention vectors from prefill; reused during decode to avoid recomputation. (Ch 9)

**Logits** — Raw model output before softmax; larger logit = higher probability for that token. (Ch 2)

**Logprobs** — Log-scale token probabilities; useful for classification, debugging, evaluation. (Ch 2)

**LoRA (Low-Rank Adaptation)** — PEFT technique: factorize weight matrix W into A×B; only update A and B; merge back at inference for zero latency overhead. (Ch 7)

**MBU (Model Bandwidth Utilization)** — Percentage of theoretical memory bandwidth being used. (Ch 9)

**MFU (Model FLOP/s Utilization)** — Percentage of theoretical peak FLOPS being used; good training MFU > 50%. (Ch 9)

**Masked LM** — Language model that fills in blanks using context before and after; used for classification/understanding (BERT). (Ch 1)

**Model drift** — Silent degradation caused by API provider updating the underlying model without announcement. (Ch 4, 10)

**Model gateway** — Abstraction layer over multiple model APIs; provides routing, auth, rate limiting, cost tracking. (Ch 10)

**Model merging** — Combining weights from multiple finetuned models without retraining; experimental but promising. (Ch 7)

**MoE (Mixture-of-Experts)** — Sparse model architecture where only a subset of "experts" is active per token; Mixtral 8x7B is an example. (Ch 2)

**NIAH (Needle in a Haystack)** — Evaluation inserting information at different context positions; models degrade at middle positions. (Ch 5)

**Orchestrator** — Tool that manages component chaining in AI pipelines (LangChain, LlamaIndex, Haystack). (Ch 10)

**PEFT (Parameter-Efficient Finetuning)** — Finetuning techniques that achieve near full-finetuning quality with far fewer trainable parameters. (Ch 7)

**Perplexity (PPL)** — Exponential of cross entropy; measures uncertainty in next-token prediction. PPL = e^H(P,Q). (Ch 3)

**Prefill** — Parallel processing of all input tokens; compute-bound; generates KV cache for decoding. (Ch 2, 9)

**Prompt caching** — Reuse prefilled KV cache for repeated system prompts; reduces cost for common prefixes. (Ch 9)

**Prompt decomposition** — Breaking complex prompts into subtask prompts chained together. (Ch 5)

**Prompt injection** — Attack where malicious instructions in user input or retrieved context manipulate model behavior. (Ch 5)

**Query rewriting** — Reformulating ambiguous queries (especially in multi-turn conversation) to be self-contained for retrieval. (Ch 6)

**Quantization** — Reducing bits per parameter (FP16 → INT8 → INT4); reduces memory and bandwidth. (Ch 7, 9)

**RAG (Retrieval-Augmented Generation)** — Retrieve relevant context from external memory, augment the prompt, generate response. (Ch 6)

**Reciprocal Rank Fusion (RRF)** — Algorithm to combine rankings from multiple retrievers. Score(D) = Σ 1/(k + rank_i(D)). (Ch 6)

**Reranking** — Using a more expensive model to reorder initial retrieval candidates for higher precision. (Ch 6)

**Reward model** — Model trained to score (instruction, response) pairs; used in RLHF. (Ch 2)

**RLHF (Reinforcement Learning from Human Feedback)** — Post-training alignment: train reward model on pairwise comparisons → optimize base model via PPO to maximize reward. (Ch 2)

**Self-supervision** — Training where labels are inferred from input data; enables LLM scale without manual labeling. (Ch 1, 2)

**Semantic caching** — Cache outputs for semantically similar queries; requires embedding + vector search + threshold tuning. (Ch 10)

**SFT (Supervised Finetuning)** — Train on (instruction, response) demonstration data to shift model from completion to conversational behavior. (Ch 2, 7)

**Soft prompts** — Trainable continuous vectors prepended to input; not human-readable; crossover between prompting and finetuning. (Ch 7)

**Speculative decoding** — Small draft model generates N candidate tokens; large model verifies all in one pass; speeds decoding. (Ch 9)

**Temperature** — Sampling parameter; divides logits before softmax. T=0: greedy/deterministic; T=0.7: creative. (Ch 2)

**TTFT (Time to First Token)** — Latency before user sees the first output; dominated by prefill time. (Ch 9)

**TPOT (Time Per Output Token)** — Time between consecutive output tokens; dominated by decode speed. (Ch 9)

**Token** — Basic unit of language models; GPT-4 uses ~¾ word per token on average. (Ch 1)

**Top-k sampling** — Sample only from k highest-logit tokens; reduces vocabulary computation. (Ch 2)

**Top-p (nucleus) sampling** — Sample from the smallest set of tokens whose cumulative probability ≥ p; dynamically adjusts to context. Common: 0.9–0.95. (Ch 2)

**Transfer learning** — Knowledge gained from one task accelerates learning for a related task; finetuning is a form of transfer learning. (Ch 7)

**Vector database** — Stores embeddings for ANN similarity search; key infrastructure for RAG. (Ch 6)
