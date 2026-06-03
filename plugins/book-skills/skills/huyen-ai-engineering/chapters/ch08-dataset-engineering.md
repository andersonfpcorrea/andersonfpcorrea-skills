# Chapter 8: Dataset Engineering

## Core Idea
Data quality beats data quantity; 10K carefully crafted examples outperform 100K noisy ones, and the three golden goals for training data are quantity, quality, and diversity.

## Frameworks Introduced

- **Three Golden Goals for Training Data**: Quantity × Quality × Diversity.
  - Quantity: enough examples to learn behavior patterns.
  - Quality: relevant, aligned, consistent, formatted, unique, compliant.
  - Diversity: covers the range of inputs and edge cases the model will encounter.

- **Data-Centric vs. Model-Centric AI**:
  - Model-centric: improve performance by enhancing model architecture/scale.
  - Data-centric: improve performance by improving training data (deduplication, quality filtering, synthesis).
  - Both are needed; data-centric focus is growing.

- **Data Quality Six Criteria**:
  1. Relevant (to the target task)
  2. Aligned with task requirements (correct labels, factual consistency)
  3. Consistent (same task → same annotation style)
  4. Correctly formatted (follows expected schema)
  5. Unique (no near-duplicates)
  6. Compliant (no PII, no copyright violations, no toxic content)

- **Data Acquisition Hierarchy**:
  1. Existing public datasets (cheapest)
  2. Scraping + filtering from web
  3. Human annotation (expensive, high quality)
  4. AI-assisted annotation (fast, review required)
  5. Synthetic data generation (scalable, risk of hallucinated errors)

## Key Concepts

- **Demonstration data (SFT)**: (prompt, response) pairs showing the model desired behavior. Quality >> quantity.
- **Preference data (RLHF/DPO)**: (instruction, winning response, losing response) triples; requires comparative labeling.
- **Reward model training data**: ((instruction, response), score) pairs.
- **Synthetic data**: AI-generated training data; scales cheaply but risks introducing model biases and errors.
- **Model collapse**: Recursively training on AI-generated data degrades performance over time (Shumailov et al., 2023).
- **Data deduplication**: Removing near-duplicate examples; prevents overfitting to repeated patterns; uses MinHash or n-gram similarity.
- **Data contamination**: Test data from public benchmarks leaking into training data; causes inflated benchmark scores.
- **CoT training data**: Training examples with step-by-step reasoning; harder to generate but substantially improves reasoning (LIMA paper: 1K examples doubled performance on CoT tasks).
- **Tool use training data**: Examples of task → tool invocation sequence; often requires simulation or expert annotation.
- **Single-turn vs. multi-turn data**: Single-turn trains response quality; multi-turn trains task-solving through conversation.
- **LIMA principle**: 1,000 carefully curated (prompt, response) pairs with Llama 65B matches or exceeds GPT-4 in 43% of cases (human judges).
- **Data flywheel**: Cycle where production usage generates feedback → feedback improves model → better model generates more usage.

## Mental Models

- Think of dataset curation like cooking: quality = ingredient freshness, coverage = right mix of ingredients, quantity = enough ingredients.
- "Garbage in, garbage out" is especially true for LLM finetuning — low-quality data can worsen hallucinations.
- Prefer curating 1K high-quality examples over 100K noisy ones (LIMA principle).
- Use AI-assisted annotation to scale human annotation, not replace it entirely.

## Reference Tables

**Data format by training phase:**

| Training phase | Data format | Key challenge |
|---|---|---|
| Self-supervised pre-training | Raw text sequences | Scale (trillions of tokens) |
| Supervised finetuning (SFT) | (instruction, response) pairs | Quality; annotation cost |
| Preference finetuning (RLHF) | (instruction, winning, losing) | Comparative labeling |
| Reward model training | (instruction, response, score) | Consistent scoring |
| Continued pre-training | Domain-specific raw text | Relevance to target domain |

**AI vs. human annotation trade-offs:**

| | Human annotation | AI annotation |
|---|---|---|
| Cost | High ($10+/example for complex tasks) | Low |
| Speed | Slow | Fast |
| Quality | High (with good labelers) | Variable; needs review |
| Bias risk | Demographic bias | Model bias |
| Complex tasks | Required for nuanced safety | Often insufficient |

## Anti-patterns

- **Quantity over quality**: 100K noisy examples consistently underperform 10K clean ones; invest in quality control.
- **Using AI-generated data unchecked**: Model collapse risk; always have human review for quality-critical tasks.
- **Ignoring data coverage**: Even high-quality data from a narrow distribution will create blind spots.
- **Not tracking data provenance**: Can't audit quality issues later if you don't know where data came from.
- **Skipping deduplication**: Duplicate examples cause overfitting and distort evaluation metrics.
- **Human-generated annotations for tool use**: Humans describe what they'd do, not necessarily what's efficient for AI; use simulation.

## Key Takeaways

1. Data quality > data quantity; 10K curated examples beat 100K noisy ones (LIMA principle).
2. Three golden goals: quantity, quality, diversity — all three must be balanced.
3. Synthetic data scales annotation but risks model collapse if used without curation.
4. Build the data flywheel: production feedback → annotation → improved model → better product.
5. Data compliance is non-negotiable: deduplicate, remove PII, check for toxic content, verify copyright.

## Connects To

- **Ch 2**: Pre-training data recipes (multilingual, domain-specific) covered here at a technical level.
- **Ch 7**: Finetuning techniques use data in SFT and preference finetuning formats described here.
- **Ch 3**: Data quality evaluation uses similarity metrics for deduplication and quality assessment.
- **Ch 10**: User feedback collection creates the training data for the data flywheel.
