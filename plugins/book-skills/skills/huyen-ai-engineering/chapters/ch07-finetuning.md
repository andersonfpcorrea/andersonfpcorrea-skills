# Chapter 7: Finetuning

## Core Idea
Finetuning is for form (output style, format, behavior alignment), not for facts; the memory bottleneck is the primary engineering challenge, and LoRA is the dominant technique for overcoming it.

## Frameworks Introduced

- **"Finetuning is for form, RAG is for facts"**: RAG adds knowledge; finetuning changes how a model behaves.
  - Use finetuning when: model has behavioral issues (wrong format, irrelevant style, unsafe outputs).
  - Use RAG when: model lacks information needed to answer correctly.

- **Adaptation Decision Framework** (progressive):
  1. Zero-shot prompting
  2. Few-shot prompting (add 1–50 examples)
  3. Simple RAG (term-based retrieval, e.g. BM25)
  4a. Advanced RAG (embedding-based) OR
  4b. Finetuning (for behavioral issues)
  5. RAG + Finetuning together

- **LoRA (Low-Rank Adaptation)**: Factorize weight matrix W (n×m) into product of A (n×r) and B (r×m); only update A and B during finetuning.
  - New weight: `W' = W + (α/r) × W_AB`
  - r = LoRA rank; smaller r = fewer parameters = less memory.
  - Merges back into W at inference; no latency overhead (unlike original adapters).

- **PEFT (Parameter-Efficient Finetuning)**: Achieve near full-finetuning performance with orders-of-magnitude fewer trainable parameters.
  - Two families: adapter-based (LoRA, IA3) and soft prompt-based (prefix-tuning, prompt-tuning, P-Tuning).

## Key Concepts

- **Full finetuning**: Update all model parameters; requires memory = weights + activations + gradients + optimizer states.
- **Partial finetuning**: Freeze early layers, update last N; parameter-inefficient (requires ~25% trainable params to match full finetuning quality).
- **PEFT**: Parameter-efficient methods that achieve full-finetuning quality with << trainable parameters.
- **LoRA rank (r)**: Controls number of trainable parameters; smaller r = less memory, potentially lower quality.
- **Adapter-based methods**: Insert trainable modules (LoRA, IA3) — additive parameters.
- **Soft prompt-based methods**: Prepend trainable vectors to input (prefix-tuning, prompt-tuning) — not human-readable.
- **Gradient checkpointing / activation recomputation**: Don't store activations; recompute during backward pass to save memory at cost of compute.
- **Mixed precision training**: Some ops in FP32, some in FP16 or BF16; balances numerical stability vs. memory.
- **Quantization**: Reduce bits per parameter (FP32 → FP16 → INT8 → INT4); shrinks model size and speeds inference.
- **Model merging**: Combine weights from multiple finetuned models into a single model without retraining.
- **Continued pre-training**: Self-supervised finetuning on domain-specific data before supervised finetuning; cheap way to add domain knowledge.
- **FP32 / FP16 / BF16 / INT8**: Numerical formats; FP32 = 4B/param, FP16 = 2B/param, BF16 = 2B/param (better range for training), INT8 = 1B/param.

## Mental Models

- Think of finetuning as "unlocking what the model already knows but can't easily express."
- LoRA is the de facto standard; use it unless you have specific reasons for other PEFT methods.
- Memory budget = weights + activations + gradients + optimizer states; reduce each component independently.
- Soft prompts are a middle ground between hard prompt engineering and full finetuning.

## Reference Tables

**Memory math for finetuning:**

| Component | Formula |
|---|---|
| Model weights | N × bytes/param |
| Activations (approx.) | 20% of model weights |
| Gradients | N_trainable × bytes/param |
| Adam optimizer states | N_trainable × 2 × bytes/param |
| **Total for inference** | N × M × 1.2 |
| **Total for full finetuning (Adam, FP16)** | N × 2 + N × 6 ≈ N × 8 bytes |

**Numerical formats:**

| Format | Bits | Bytes/param | Use case |
|---|---|---|---|
| FP32 | 32 | 4 | Training (high precision) |
| FP16 | 16 | 2 | Mixed precision, inference |
| BF16 | 16 | 2 | Training (better range than FP16) |
| INT8 | 8 | 1 | Quantized inference |
| INT4 | 4 | 0.5 | Aggressive quantized inference |

**RAG vs. Finetuning performance (Ovadia et al., 2024, MMLU benchmark):**

| Model | Base | Base + RAG | Finetuned | Finetuned + RAG |
|---|---|---|---|---|
| Mistral-7B | 0.481 | 0.875 | 0.504 | 0.588 |
| Llama 2-7B | 0.353 | 0.585 | 0.219 | 0.392 |

(RAG outperforms finetuning for knowledge-intensive tasks)

## Code Examples

```python
# LoRA weight update formula
# W' = W + (alpha/r) * A @ B
# A: (n, r), B: (r, m), r << min(n, m)

# Memory estimate for inference
def inference_memory_gb(params_billions, bytes_per_param=2):
    return params_billions * 1e9 * bytes_per_param * 1.2 / 1e9

# Example: 13B model in FP16
print(inference_memory_gb(13))  # ~31.2 GB

# Memory estimate for full finetuning with Adam in FP16
def finetuning_memory_gb(params_billions, bytes_per_param=2):
    # weights + gradients + Adam 2x states
    return params_billions * 1e9 * bytes_per_param * (1.2 + 3) / 1e9

print(finetuning_memory_gb(7))  # ~58 GB for 7B model
```

## Anti-patterns

- **Finetuning for knowledge gaps**: RAG is faster and cheaper; finetuning on factual data can actually worsen hallucinations if data quality is low.
- **Skipping the progressive adaptation path**: Teams that jump straight to finetuning often discover prompting would have been sufficient.
- **Full finetuning on consumer hardware**: 7B model full finetuning needs ~58 GB; use LoRA instead.
- **Ignoring original adapters' latency overhead**: Original Houlsby adapters add inference latency; LoRA eliminates this by merging back.
- **Confusing soft prompts with hard prompts**: Soft prompts are continuous embeddings, not text; they require training infrastructure.

## Key Takeaways

1. "Finetuning is for form, RAG is for facts" — use RAG first for knowledge gaps.
2. LoRA is the dominant PEFT technique; achieves near full-finetuning performance with 1–3% trainable parameters.
3. Memory for full finetuning ≈ 4× memory for inference (due to gradients and optimizer states).
4. Mixed precision (FP16/BF16 for weights, FP32 for master weights) balances stability and memory.
5. Follow the progressive adaptation path: prompting → few-shot → RAG → finetuning.

## Connects To

- **Ch 2**: SFT and preference finetuning types introduced here are applied in Ch 7.
- **Ch 6**: RAG vs. finetuning decision framework: knowledge (RAG) vs. behavior (finetuning).
- **Ch 8**: Dataset engineering provides the training data needed for finetuning.
- **Ch 9**: Quantization (inference optimization) builds on numerical representation concepts here.
