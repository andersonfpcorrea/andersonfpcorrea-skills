# Chapter 9: Inference Optimization

## Core Idea
Autoregressive language model inference is memory bandwidth-bound during decoding; optimization requires understanding the prefill/decode split and targeting the right bottleneck at model, hardware, and service levels.

## Frameworks Introduced

- **Prefill/Decode Split**:
  - Prefill: process all input tokens in parallel → compute-bound.
  - Decode: generate one output token at a time → memory bandwidth-bound.
  - In production: decouple on separate machines with distinct hardware optimization.

- **Two Computational Bottlenecks**:
  - Compute-bound: time-to-complete limited by arithmetic operations (FLOPs).
  - Memory bandwidth-bound: time-to-complete limited by data transfer rate (memory ↔ processor).
  - Rooflne chart visualizes which regime you're in per workload.

- **Model-Level Optimizations**:
  1. Quantization: reduce bits/param (FP16 → INT8 → INT4).
  2. Knowledge distillation: train smaller student model from larger teacher.
  3. Pruning: remove unimportant weights (structured or unstructured).
  4. Efficient architecture: attention alternatives (FlashAttention, MQA, GQA).

- **Inference Service Optimizations**:
  1. KV cache: store computed key-value vectors to avoid recomputation.
  2. Prompt caching: reuse prefilled KV states for common system prompts.
  3. Continuous batching: dynamically group requests to maximize throughput.
  4. Speculative decoding: small draft model generates candidates, large model verifies in parallel.

## Key Concepts

- **MFU (Model FLOP/s Utilization)**: Actual throughput / theoretical peak throughput; good training MFU > 50%.
- **MBU (Model Bandwidth Utilization)**: Memory bandwidth used / theoretical peak bandwidth. Formula: `(param_count × bytes/param × tokens/s) / theoretical_bandwidth`.
- **Throughput**: Output tokens per second (TPS) across all users; higher throughput = lower cost.
- **TTFT (Time to First Token)**: Latency before user sees the first output token; dominated by prefill.
- **TPOT (Time Per Output Token)**: Latency between each subsequent token; dominated by decode.
- **Goodput**: Requests per second that satisfy the SLO (latency constraint); preferred over throughput alone.
- **KV cache**: Cached key-value vectors from prefill; reused during decode to avoid recomputation. Core memory bottleneck during inference.
- **Prompt caching**: Reuse KV cache from identical prefix (system prompt); reduces cost for repeated prompts.
- **Continuous batching**: Instead of waiting for all requests in a batch to finish, insert new requests into ongoing batches; improves GPU utilization.
- **Speculative decoding**: Draft small model guesses next N tokens; large model verifies all in one forward pass; effective when draft model has high acceptance rate.
- **Quantization**: Post-training reduction of weight precision. `INT8` = half the memory of `FP16`; `INT4` = quarter memory.
- **Knowledge distillation**: Smaller student model trained to mimic a larger teacher model's outputs.
- **Flash Attention**: Memory-efficient attention implementation; computes attention in tiles to avoid materializing full N×N attention matrix; near-linear memory.
- **GQA (Grouped Query Attention)**: Multiple query heads share key-value heads; reduces KV cache size significantly.
- **MQA (Multi-Query Attention)**: All query heads share a single KV head; more extreme KV cache reduction.
- **GPU vs. TPU vs. specialized accelerators**: GPUs dominate training; inference chips (AWS Inferentia, Apple ANE) optimized for lower precision and faster memory access.

## Mental Models

- Optimize for the bottleneck: decoding is memory-bandwidth-bound, so quantization (fewer bytes = less bandwidth) helps directly.
- KV cache memory grows linearly with sequence length and batch size; long context = large KV cache = less memory for batch size.
- Latency/throughput tradeoff: techniques like batching improve throughput but increase TTFT and TPOT.
- Start with prompt caching if you have long, repeated system prompts — free 50–90% cost reduction.

## Reference Tables

**Inference metrics:**

| Metric | Formula | Good for |
|---|---|---|
| Throughput | output tokens/s | Batch/async workloads |
| TTFT | time to first token | Interactive / streaming |
| TPOT | time per output token | Response fluency |
| Goodput | requests/s satisfying SLO | Production SLO compliance |
| MFU | actual_throughput / peak_throughput | Hardware efficiency |
| MBU | bandwidth_used / peak_bandwidth | Memory efficiency |

**MBU formula:**
```
MBU = (param_count × bytes_per_param × tokens_per_s) / theoretical_bandwidth_GB_per_s
```
Example: 7B params, FP16 (2 bytes), 100 tokens/s on A100 (2 TB/s):
`(7B × 2 × 100) / 2000 = 700/2000 = 35% MBU`

**Online vs. Batch inference:**

| | Online API | Batch API |
|---|---|---|
| Optimization target | Latency | Cost/throughput |
| Cost (OpenAI/Google) | Full price | ~50% discount |
| Turnaround | Seconds | Hours |
| Use case | Chatbots, copilots | Data pipelines, offline processing |

**Model MFU examples (from PaLM paper):**

| Model | Accelerator | MFU |
|---|---|---|
| GPT-3 175B | V100 | 21.3% |
| Gopher 280B | 4096 TPU v3 | 32.5% |
| PaLM 540B | 6144 TPU v4 | 46.2% |

## Anti-patterns

- **Relying on nvidia-smi GPU utilization**: Reports % time GPU is active, not % of peak FLOPS used; use MFU instead.
- **Ignoring the prefill/decode split**: Different bottlenecks require different optimizations; optimizing decode with compute hardware is wasteful.
- **Adding more context without KV cache budget planning**: Long context means large KV cache means smaller effective batch size.
- **Optimizing throughput at the cost of latency SLO**: Use goodput as the target metric for production workloads.

## Key Takeaways

1. Autoregressive decoding is memory bandwidth-bound; quantization directly reduces bandwidth consumption.
2. KV cache is the key inference optimization; prompt caching extends it for repeated prefixes.
3. Continuous batching dramatically improves GPU utilization vs. static batching.
4. Speculative decoding speeds up decode without quality loss when a fast draft model is available.
5. Use MFU/MBU to measure hardware efficiency; online APIs for latency-sensitive, batch APIs for cost-sensitive workloads.

## Connects To

- **Ch 2**: Prefill/decode architecture and KV vectors introduced here are the foundation for inference optimization.
- **Ch 7**: Quantization (inference optimization) builds on numerical representation concepts from finetuning.
- **Ch 10**: Caching (KV cache, prompt cache, semantic cache) appears in the system architecture.
