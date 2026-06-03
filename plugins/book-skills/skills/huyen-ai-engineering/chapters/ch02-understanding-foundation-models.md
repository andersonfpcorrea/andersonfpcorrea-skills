# Chapter 2: Understanding Foundation Models

## Core Idea
Foundation model behavior is determined by training data distribution, architecture choices, model scale, and post-training alignment; sampling strategy is the most underrated lever for boosting performance.

## Frameworks Introduced

- **Chinchilla Scaling Law**: For compute-optimal training, number of training tokens ≈ 20× model parameter count; scale model size and data size equally (both should double together).
  - Use when: deciding model size and dataset size given a compute budget.
  - Key insight: GPT-3 (175B params, 300B tokens) was undertrained; Chinchilla (70B params, 1.4T tokens) outperformed it.

- **Three Training Scale Numbers**: Parameters (learning capacity) × Training tokens (knowledge breadth) × FLOPs (compute cost).
  - Quick signal for comparing model scale and cost.

- **Post-Training Pipeline**: Pre-training (self-supervision) → SFT (demonstration data) → Preference finetuning (RLHF/DPO).
  - Pre-training = knowledge acquisition; post-training = behavior alignment.
  - InstructGPT: 98% compute for pre-training, only 2% for post-training.

- **Sampling Strategies**: Temperature → Top-k → Top-p (nucleus) → Min-p.
  - Temperature controls creativity/predictability tradeoff by scaling logits before softmax.
  - Top-p dynamically sets candidate set size; Top-k uses fixed set size.

- **Best-of-N Strategy**: Generate N outputs, score with reward model, pick the highest scored.
  - Use when: you want RLHF quality without full RL training; Stitch Fix and Grab use this.

## Key Concepts

- **Parameters**: Model variables updated during training; proxy for learning capacity.
- **Logits**: Raw model output before softmax; larger logit = higher probability for that token.
- **Logprobs**: Log-scale probabilities; useful for classification tasks and debugging model behavior.
- **Temperature**: Constant dividing logits before softmax; T→0 = greedy (deterministic), T↑ = more creative/random. T=0.7 recommended for creative tasks.
- **Top-k sampling**: Compute softmax only over k highest-logit tokens; reduces vocabulary computation.
- **Top-p (nucleus) sampling**: Sample from smallest token set whose cumulative probability ≥ p; dynamically adjusts to context. Common: 0.9–0.95.
- **Greedy sampling**: Always pick highest-probability token; creates boring/repetitive outputs.
- **KV vectors**: Key-value vectors for attention mechanism; grow linearly with sequence length — core memory bottleneck.
- **Mixture-of-Experts (MoE)**: Sparse model where only a subset of experts is active per token; Mixtral 8x7B uses only 12.9B of 46.7B active parameters.
- **SFT (Supervised Finetuning)**: Train on (prompt, response) demonstration data to shift from completion to conversation behavior.
- **RLHF**: Train reward model on preference comparisons (pairwise better/worse), then optimize base model via PPO to maximize reward.
- **DPO (Direct Preference Optimization)**: Alternative to RLHF; used by Llama 3; simpler but less flexible.
- **Emergent abilities**: Capabilities that appear only at scale; not observable in smaller models.
- **Inverse scaling**: Rare phenomenon where larger models perform worse; affects memorization and tasks with strong priors.

## Mental Models

- Think of pre-training as "reading to acquire knowledge" and post-training as "learning how to use that knowledge."
- Think of the Shoggoth meme: raw pre-trained model is the monster, SFT makes it socially acceptable, preference finetuning adds the friendly face.
- Use temperature = 0 for consistent/deterministic outputs (classification, structured tasks); use higher temperature (0.7–1.0) for creative tasks.
- For debugging: check logprobs — if they look random, the model hasn't learned.

## Reference Tables

**Llama model architecture dimensions:**

| Model | Transformer blocks | Model dim | Feedforward dim | Vocab size |
|---|---|---|---|---|
| Llama 2-7B | 32 | 4,096 | 11,008 | 32K |
| Llama 2-13B | 40 | 5,120 | 13,824 | 32K |
| Llama 2-70B | 80 | 8,192 | 22,016 | 32K |
| Llama 3-7B | 32 | 4,096 | 14,336 | 128K |
| Llama 3-70B | 80 | 8,192 | 28,672 | 128K |
| Llama 3-405B | 126 | 16,384 | 53,248 | 128K |

**Memory for model weights (inference):**
- Formula: `N × M × 1.2` where N = params, M = bytes/param
- 13B params at FP16 (2B/param): 13B × 2 × 1.2 ≈ 31.2 GB

## Anti-patterns

- **Overtrained small model vs. undertrained large model**: Bigger model trained on too little data underperforms a well-trained smaller model.
- **Mixing up FLOPs and FLOP/s**: FLOPs = total operations for a task; FLOP/s = machine peak performance. Don't confuse them.
- **Assuming all ACID-labeled databases provide serializable isolation**: Check isolation level explicitly.

## Key Takeaways

1. Chinchilla law: ~20 training tokens per parameter is compute-optimal; most large models are undertrained.
2. Post-training costs only 2% of compute but transforms a raw model into a conversational assistant.
3. Temperature is the easiest sampling lever: T=0 for determinism, T=0.7 for creative tasks.
4. Top-p is preferred over top-k in practice despite similar theory — it's more context-adaptive.
5. Best-of-N is a practical alternative to RLHF for teams that can't afford RL training.

## Connects To

- **Ch 7**: Finetuning details including PEFT and LoRA build on SFT introduced here.
- **Ch 9**: Inference optimization builds on prefill/decode architecture and KV vectors.
- **Ch 3–4**: Language modeling metrics (perplexity, cross entropy) connect to training objectives.
