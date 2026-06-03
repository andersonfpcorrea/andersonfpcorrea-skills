# Chapter 1: Introduction to Building AI Applications with Foundation Models

## Core Idea
AI engineering — building applications on top of existing foundation models — has emerged as a fast-growing discipline because foundation models have made powerful AI capabilities accessible via API while lowering the barrier to entry for developers.

## Frameworks Introduced

- **Three Factors for AI Engineering Growth**: General-purpose AI capabilities (models can do more tasks), increased AI investments, and low entrance barrier (model-as-a-service APIs).
  - Use this to explain why the shift from ML engineering to AI engineering happened now, not earlier.

- **Three-Layer AI Stack**: Application development → Model development → Infrastructure.
  - Application development: prompt engineering, evaluation, AI interfaces.
  - Model development: modeling/training, dataset engineering, inference optimization.
  - Infrastructure: serving, compute, monitoring.

- **Two Model Adaptation Categories**:
  - Prompt-based (no weight update): prompt engineering, RAG — easier, less data, more portable.
  - Finetuning (weight update): higher quality ceiling, handles complex tasks, requires data and compute.

## Key Concepts

- **Foundation models**: Large-scale models (LLMs and LMMs) capable of general-purpose tasks; trained via self-supervision on massive datasets.
- **Self-supervision**: Model infers its own training labels from input sequences; enables scaling without manual labeling.
- **Token**: Basic unit of language models; GPT-4 uses ~¾ word per token on average.
- **Autoregressive LM**: Generates next token using only preceding tokens; dominant for text generation.
- **Masked LM**: Fills in blanks using tokens before and after; used for classification/understanding.
- **Pre-training**: Training from scratch using self-supervision; resource-intensive (98% of compute for InstructGPT).
- **Finetuning / Post-training**: Continuing training on a previously trained model; cheaper, requires less data.
- **Model as a service**: Models exposed via APIs that return outputs for queries; no infrastructure required.
- **AI engineering vs. ML engineering**: AI engineering focuses on model adaptation and evaluation; ML engineering focuses on model development.

## Mental Models

- Think of a foundation model as a library of programs; prompt engineering finds the right activating prompt.
- Use prompt-based techniques first when: you can iterate fast and task complexity is modest; switch to finetuning when performance ceilings are hit.
- The new AI engineering workflow rewards those who can iterate fast — build product first, then invest in data and models once the product shows promise.
- AI engineering is less about modeling and more about adapting and evaluating models.

## Anti-patterns

- **Treating AI engineering as just prompt fiddling**: Systematic evaluation, experiment tracking, and ML knowledge are still essential for production.
- **Colloquially calling context injection "training"**: Feeding journal entries into ChatGPT is prompt engineering, not training.
- **Building without evaluating**: An application deployed but unmonitored is often worse than one never deployed.

## Reference Tables

**How model development responsibilities change with AI engineering:**

| Category | Building with traditional ML | Building with foundation models |
|---|---|---|
| Modeling and training | ML knowledge required | Nice-to-have, not must-have |
| Dataset engineering | Feature engineering, tabular data | Deduplication, tokenization, quality control |
| Inference optimization | Important | Even more important |

**Use case categories (from analysis of 205 open source AI apps):**

| Category | Consumer examples | Enterprise examples |
|---|---|---|
| Coding | Code completion | Internal dev tools |
| Writing | Email, social media | Copywriting, SEO, reports |
| Conversational bots | General chatbot, AI companion | Customer support, copilots |
| Information aggregation | Summarization | Market research |
| Workflow automation | Travel planning | Data extraction, lead gen |

## Key Takeaways

1. Foundation models enable AI engineering by providing general-purpose capabilities via APIs — the "model as a service" model.
2. The 3-layer AI stack (application, model, infrastructure) is the lens for understanding where AI engineering work happens.
3. Start with prompt-based adaptation; only invest in finetuning when prompt engineering hits its ceiling.
4. Evaluation is the biggest bottleneck to AI adoption; building reliable evaluation pipelines unlocks more applications.
5. AI engineering is closer to full-stack engineering than to traditional ML engineering.

## Connects To

- **Ch 2**: Foundation model internals (training data, architecture, sampling) — what makes these models work.
- **Ch 3–4**: Evaluation methodology — central to AI engineering.
- **Ch 5**: Prompt engineering — the first adaptation technique.
- **Ch 7**: Finetuning — when prompt-based techniques are insufficient.
