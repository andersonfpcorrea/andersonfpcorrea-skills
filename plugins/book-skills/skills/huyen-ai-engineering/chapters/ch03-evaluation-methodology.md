# Chapter 3: Evaluation Methodology

## Core Idea
Evaluating foundation models is harder than evaluating classical ML because outputs are open-ended; systematic automatic evaluation — especially AI as a judge — is now essential infrastructure, not an afterthought.

## Frameworks Introduced

- **Four Evaluation Methods** (in order of automation difficulty):
  1. Functional correctness — exact verification (code, SQL, games)
  2. Exact match — binary match against reference responses
  3. Lexical similarity — n-gram/edit-distance overlap (BLEU, ROUGE)
  4. Semantic similarity — embedding cosine similarity (BERTScore)
  5. AI as a judge — LLM evaluates another LLM's output

- **AI as a Judge** (three modes):
  1. Pointwise: score output in isolation (1–5 scale)
  2. Reference-based comparison: match generated vs. reference response (True/False)
  3. Pairwise: compare two responses and pick the better one (A/B)
  - Use pairwise for generating preference data, test-time compute, and comparative leaderboards.

- **pass@k Metric** (for code evaluation): Fraction of problems solved if k code samples are generated per problem. pass@1 < pass@3 < pass@10 by design.

## Key Concepts

- **Cross entropy**: How difficult it is for a model to predict the next token; lower is better. `H(P,Q) = H(P) + KL(P||Q)`.
- **Perplexity (PPL)**: Exponential of cross entropy; measures uncertainty in next-token prediction. `PPL = e^H(P,Q)`.
- **Bits-per-character (BPC) / Bits-per-byte (BPB)**: Normalized cross entropy for cross-model comparison.
- **Embedding**: Numerical vector representation capturing semantic meaning. Typical size: 100–10,000 dimensions.
- **Cosine similarity**: `(A·B) / (||A|| × ||B||)`. Range: -1 to 1; identical embeddings = 1.
- **Lexical similarity**: Overlap of tokens/n-grams; BLEU measures precision of n-grams, ROUGE measures recall.
- **Exact match**: Binary; works only for short, unambiguous expected answers.
- **Functional correctness**: Execute generated code against unit tests; used in HumanEval (pass@k), BIRD-SQL.
- **Benchmark saturation**: When a model achieves near-perfect scores, benchmarks must be replaced (GLUE → SuperGLUE, MMLU → MMLU-Pro).
- **Reference-free metrics**: Evaluate quality without ground truth; enables production evaluation where no reference exists.
- **Pointwise evaluation**: Scoring each response independently; high variance across raters.
- **Pairwise evaluation**: Comparing two responses; lower variance, preferred for preference data.

## Mental Models

- Use functional correctness when possible — it's the gold standard and is automatable.
- Use AI as a judge when: no reference data exists, task is open-ended, you need fast iteration.
- Use lexical similarity (BLEU/ROUGE) for legacy benchmarks and translation tasks; prefer semantic similarity or AI judges for new work.
- Think of perplexity as "how many fair dice does the model think it's rolling?" — lower = more confident.

## Reference Tables

**Evaluation method comparison:**

| Method | Requires reference | Automatable | Best for |
|---|---|---|---|
| Functional correctness | No | Yes | Code, SQL, game bots |
| Exact match | Yes | Yes | Short/closed answers |
| Lexical similarity | Yes | Yes | Translation (BLEU/ROUGE) |
| Semantic similarity | Yes | Yes | Open-ended, paraphrase tolerance |
| AI as a judge | Optional | Yes | Any open-ended task |
| Human evaluation | No | No | Ground truth, audit |

**Embedding model sizes:**

| Model | Embedding size |
|---|---|
| BERT base | 768 |
| BERT large | 1024 |
| OpenAI text-embedding-3-small | 1536 |
| OpenAI text-embedding-3-large | 3072 |
| Cohere Embed v3 | 1024 |

## Code Examples

```python
# Functional correctness evaluation (HumanEval style)
def evaluate_pass_at_k(problems, model, k=1):
    solved = 0
    for problem in problems:
        samples = [model.generate(problem.prompt) for _ in range(k)]
        if any(run_tests(sample, problem.test_cases) for sample in samples):
            solved += 1
    return solved / len(problems)  # pass@k score

# AI-as-judge prompt (pairwise comparison)
JUDGE_PROMPT = """Given the following question and two answers, evaluate which
answer is better. Output A or B.
Question: {question}
A: {answer_a}
B: {answer_b}
The better answer is:"""
```

## Anti-patterns

- **Eyeballing results**: Ad hoc evaluation based on a few go-to prompts leads to unreliable conclusions; systematic evaluation is required.
- **Optimizing BLEU for code generation**: BLEU scores for correct and incorrect code solutions are similar; BLEU is not a proxy for functional correctness.
- **Missing reference responses**: Low reference coverage causes correct model outputs to be penalized (Fuyu image captioning example).
- **Benchmark cherry-picking**: Google/Gemini MMLU story — CoT@32 vs 5-shot changes the ranking dramatically; always compare under identical conditions.

## Key Takeaways

1. Evaluation is the biggest bottleneck in AI engineering; invest in it before building features.
2. Functional correctness is the gold standard — automate it wherever possible.
3. AI as a judge achieves ~85% agreement with humans on MT-Bench; it is now the de facto industry standard for subjective evaluation.
4. Cross entropy/perplexity measures training quality; for production quality, you need task-specific evaluation.
5. Build a comprehensive evaluation pipeline before deploying; a deployed application you can't evaluate is often worse than one not deployed.

## Connects To

- **Ch 4**: Uses these methods to build an application evaluation pipeline and model selection framework.
- **Ch 5**: Prompt evaluation leverages lexical/semantic similarity and AI judges.
- **Ch 6**: RAG evaluation uses retrieval quality + end-to-end generation quality.
- **Ch 8**: Data quality evaluation uses similar similarity metrics for deduplication.
