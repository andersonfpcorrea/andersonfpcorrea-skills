# Chapter 4: Evaluate AI Systems

## Core Idea
Evaluation-driven development — define evaluation criteria before building — is the practice that separates successful AI applications from speculative ones; the model selection and evaluation pipeline must be systematic, not ad hoc.

## Frameworks Introduced

- **Evaluation-Driven Development**: Define evaluation criteria before building, inspired by test-driven development. Identify what failure looks like before writing code.
  - Use when: starting any new AI application.
  - Most common enterprise applications in production have clear evaluation criteria (recommender systems, fraud detection, coding) for exactly this reason.

- **Four Evaluation Criteria Buckets**:
  1. Domain-specific capability (can the model do the task?)
  2. Generation capability (coherence, faithfulness, tone)
  3. Instruction-following capability (format compliance, length)
  4. Cost and latency (tokens, response time)

- **Model Selection Process**:
  1. Identify required capabilities → find relevant public benchmarks
  2. Check public leaderboards (LMSYS Chatbot Arena) for ranking
  3. Build private benchmark from your production data
  4. A/B test top candidates in production

- **Closed-Ended Evaluation for Non-Code Tasks**: Use multiple-choice questions instead of open-ended generation for domain capability assessment; reduces evaluation variance.

## Key Concepts

- **Evaluation criteria**: Application-specific metrics that define what "working" means.
- **Hallucination detection**: Evaluating whether outputs are supported by context (factual consistency).
- **Instruction-following capability**: Whether the model adheres to format constraints, length limits, output schema.
- **Factual consistency**: Whether the model's claims are supported by provided context; key for RAG applications.
- **Private benchmark**: Evaluation set derived from your own production data; higher fidelity than public benchmarks.
- **Public benchmark risks**: Contamination (models may have seen test data during training), saturation (benchmark scores don't differentiate strong models), and gaming (optimizing for benchmark over real quality).
- **Chatbot Arena / LMSYS**: Human-preference-based leaderboard; uses Elo ratings from pairwise comparisons; harder to game than benchmark leaderboards.
- **Model drift**: Silent performance changes when an API's underlying model is updated (GPT-3.5-turbo-0301 vs turbo-1106 had 10%+ performance differences).
- **Groundedness / faithfulness**: Whether RAG-generated responses are supported by retrieved context.
- **Close-ended task evaluation**: Easier to verify and reproduce; preferred for domain capability benchmarks.

## Mental Models

- Use public benchmarks for initial model filtering only; build private benchmarks for final selection.
- Think of evaluation in four layers: can it do the task? is the output good? does it follow instructions? is it fast and cheap enough?
- An application you cannot evaluate is a liability; before deploying, answer "how will I know if this breaks?"
- Prefer close-ended evaluation (multiple choice) for measuring domain capability; it's reproducible and cheap.

## Reference Tables

**Evaluation approach by criterion:**

| Criterion | Evaluation approach | Method |
|---|---|---|
| Code correctness | Functional correctness | pass@k |
| Math reasoning | Multiple choice / final answer | Exact match |
| Summarization quality | Reference comparison or AI judge | ROUGE / LLM |
| Factual consistency | Entailment / AI judge | NLI or prompt |
| Safety / toxicity | Classifier or AI judge | Automated |
| Instruction following | Format check | Regex / parsing |
| Latency | Measurement | p50/p95/p99 latency |

**Common public benchmarks by capability:**

| Capability | Benchmark |
|---|---|
| General knowledge | MMLU, MMLU-Pro |
| Reasoning | AGIEval, ARC-C |
| Math | GSM-8K, MATH |
| Coding | HumanEval, MBPP |
| SQL generation | Spider, BIRD-SQL |
| Long context | RULER, NIAH |

## Anti-patterns

- **Relying only on public leaderboards**: Models may be contaminated with test data; leaderboard rankings don't translate to your task.
- **Evaluating in isolation**: A prompt that improves one subtask can degrade the full system; always evaluate end-to-end.
- **No production evaluation**: Deploying without monitoring means you cannot detect model drift or quality degradation.
- **Selecting metrics that don't align to user value**: A high BLEU score or perplexity doesn't mean users are happy.

## Key Takeaways

1. Evaluation-driven development: define success criteria before building.
2. Four criteria buckets: domain capability, generation quality, instruction following, cost/latency.
3. Public benchmarks are for initial filtering; private benchmarks are for final model selection.
4. Model drift is real and silent — monitor for underlying API changes over time.
5. Close-ended evaluation (multiple choice) is more reproducible than open-ended for domain capability.

## Connects To

- **Ch 3**: Evaluation methods (AI judge, similarity metrics) used to implement these criteria.
- **Ch 5**: Prompt engineering effectiveness is evaluated using the criteria here.
- **Ch 6**: RAG system evaluation uses retrieval quality + generation quality criteria.
- **Ch 10**: Monitoring and observability translate evaluation metrics to production signals.
