# Cheatsheet — AI Engineering (Chip Huyen)

## Adaptation Technique Picker

| Situation | Technique |
|---|---|
| Fast iteration, unknown requirements | Zero-shot prompting |
| Model needs examples of desired format/style | Few-shot prompting |
| Model lacks factual/domain knowledge | RAG (start with BM25) |
| Complex retrieval needs (semantic, multi-modal) | Embedding-based / hybrid RAG |
| Model has behavioral issues (wrong format, style) | Finetuning (LoRA) |
| Both knowledge and behavior issues | RAG + Finetuning |

**Rule of thumb**: "RAG is for facts, finetuning is for form."

---

## Sampling Parameters Quick Reference

| Parameter | Effect | Typical values |
|---|---|---|
| Temperature | Creativity vs. consistency | 0 = deterministic, 0.7 = creative, 1.0+ = very random |
| Top-k | Fixed candidate set size | 50–500 |
| Top-p (nucleus) | Dynamic candidate set size | 0.9–0.95 |
| Temperature = 0 | Greedy (picks highest logit) | Use for classification, structured output |

---

## Memory Formulas

**Inference memory:**
```
N_params × bytes/param × 1.2
```
Example: 13B FP16 (2B/param): `13B × 2 × 1.2 = 31.2 GB`

**Full finetuning memory (Adam, same precision):**
```
N_params × bytes/param × (1.2 weights + 1 grad + 2 Adam states) ≈ N × 4× inference
```

**LoRA memory savings:** Trainable params = 2 × n × r (per layer), where r << n

---

## Inference Metrics at a Glance

| Metric | Measures | Formula |
|---|---|---|
| TTFT | Time to first token | wall clock to first output token |
| TPOT | Time per output token | wall clock / output tokens |
| Throughput | Output tokens/s | total_output_tokens / total_time |
| MFU | Hardware compute efficiency | actual_throughput / peak_theoretical_throughput |
| MBU | Hardware bandwidth efficiency | (params × bytes × tokens/s) / peak_bandwidth |
| Goodput | SLO-compliant requests/s | requests_meeting_SLO / time |

**Online API**: optimize for TTFT + TPOT (latency)
**Batch API**: optimize for throughput + cost (at 50% discount from major providers)

---

## Chinchilla Law Quick Reference

- **Compute-optimal**: training tokens ≈ 20 × model parameters
- **Scale both equally**: double params → double training tokens
- Notable: Llama 3 used 15T tokens for ~8B params (significantly over-trained for inference efficiency)

---

## Evaluation Method Picker

| Task type | Best evaluation |
|---|---|
| Code generation | Functional correctness (pass@k) |
| Short factual answers | Exact match |
| Translation | BLEU/ROUGE (legacy) or AI judge |
| Open-ended generation | AI as a judge (pairwise preferred) |
| RAG output | Faithfulness (AI judge) + retrieval recall |
| Instruction following | Format check (regex/parsing) |

---

## RAG Troubleshooting

| Problem | Likely cause | Fix |
|---|---|---|
| Retrieved docs irrelevant | Chunk too large, wrong retrieval method | Smaller chunks, try hybrid search |
| Context cut off at chunk boundary | No overlap | Add 10–20% overlap |
| Multi-turn conversation retrieves wrong info | Ambiguous query | Add query rewriting step |
| Vector DB spending > model API spending | Embedding-based only | Consider term-based retrieval for bulk |

---

## Architecture Component Addition Guide

| When you observe | Add this |
|---|---|
| Model lacks domain knowledge | RAG (Ch 6) |
| PII leaking to external APIs | Input guardrails (Ch 10) |
| Model generating toxic/bad outputs | Output guardrails + retry (Ch 10) |
| Want to use multiple models | Model router/gateway (Ch 10) |
| High cost from repeated similar queries | Exact cache or semantic cache (Ch 10) |
| Repeated system prompts | Prompt caching (Ch 9) |
| Complex multi-step workflows | Agent pattern (Ch 6, 10) |

---

## Finetuning vs. RAG Decision Matrix

| Situation | Preferred |
|---|---|
| Model needs current/proprietary knowledge | RAG |
| Model generates wrong format consistently | Finetuning |
| Model hallucinates factual claims | RAG first |
| Model responses misaligned with brand voice | Finetuning |
| Both knowledge and format issues | RAG first, then Finetuning |
| Limited compute budget | RAG (cheaper than finetuning) |

---

## Key Numbers to Remember

- Post-training compute share: ~2% (vs. 98% for pre-training)
- LoRA with 3% trainable params achieves within 0.4% of full finetuning (GLUE benchmark)
- LIMA: 1K examples matches GPT-4 in 43% of comparisons
- AI judge (GPT-4) agrees with humans 85% on MT-Bench (vs. 81% human-human agreement)
- Inference accounts for up to 90% of ML costs in deployed systems
- Vector DB spending can be 1/5 to 1/2 of model API spending
- Batch APIs: ~50% cost discount, hours latency vs. seconds (OpenAI, Google)
