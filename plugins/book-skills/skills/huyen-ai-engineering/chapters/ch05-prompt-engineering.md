# Chapter 5: Prompt Engineering

## Core Idea
Prompt engineering is human-to-AI communication; it requires systematic experimentation and defensive design, not just trial-and-error — exhaust prompt-based techniques before investing in finetuning.

## Frameworks Introduced

- **In-Context Learning (ICL)**: Models learn desired behavior from examples in the prompt without weight updates.
  - Zero-shot: no examples; Few-shot: 1–N examples (each = "a shot").
  - Use when: you need the model to perform a task it wasn't explicitly trained for; avoids retraining.

- **Chain-of-Thought (CoT)**: Adding "think step by step" or explicit reasoning steps to prompt; improves multi-step reasoning and reduces hallucination.
  - Zero-shot CoT: "think step by step"
  - Explicit CoT: specify the steps
  - One-shot CoT: include an example with reasoning

- **Prompt Decomposition**: Break complex prompts into subtasks, each with its own prompt; chain them together.
  - Benefits: monitoring intermediate outputs, debugging step isolation, parallelizing independent steps, using cheaper models for simple subtasks.

- **Defensive Prompt Engineering**: Three attack types to defend against:
  1. Prompt extraction (stealing system prompt)
  2. Jailbreaking (bypassing safety constraints)
  3. Prompt injection (malicious content in user input manipulates the model)

## Key Concepts

- **System prompt**: Developer-controlled instructions defining model behavior/persona. Anthropic: maintain character better throughout conversation.
- **User prompt**: Task or question from the end user.
- **Chat template**: Model-specific format for combining system and user prompts (Llama 2 vs Llama 3 have different templates — silent failures if wrong).
- **In-context learning**: Teaching the model via examples in the prompt; no weight updates.
- **Needle in a haystack (NIAH)**: Test inserting information at different positions in a long prompt; models perform better at beginning and end than middle.
- **Prompt catalog**: Versioned repository of prompts with metadata (model name, date, application, creator, sampling params).
- **Soft prompts**: Trainable continuous vectors prepended to input (not human-readable); crossover between prompting and finetuning.
- **Self-critique / self-eval**: Ask the model to check its own outputs before returning them.
- **Prompt extraction**: Attack where users manipulate the model into revealing its system prompt.
- **Jailbreaking**: Prompts that bypass model safety restrictions.
- **Prompt injection**: Malicious instructions injected via user input or retrieved context.

## Mental Models

- Prefer system prompt for instructions, user prompt for the task — matches model training.
- Put the most important instructions at the beginning and end of context (not the middle).
- Use prompt decomposition when: task is complex, you need to monitor intermediate steps, or different parts can use different models.
- Separate prompts from code (put in `prompts.py`, version in git) for reusability, testability, and collaboration.

## Code Examples

```python
# Separate prompts from code
# file: prompts.py
GPT4o_ENTITY_EXTRACTION_PROMPT = "[YOUR PROMPT]"

# file: application.py
from prompts import GPT4o_ENTITY_EXTRACTION_PROMPT

def query_openai(model_name, user_prompt):
    completion = client.chat.completions.create(
        model=model_name,
        messages=[
            {"role": "system", "content": GPT4o_ENTITY_EXTRACTION_PROMPT},
            {"role": "user", "content": user_prompt}
        ]
    )
```

```python
# Prompt with output marker to prevent continuation
prompt = """Label the following item as edible or inedible.

pineapple pizza --> edible
cardboard --> inedible
chicken -->"""
# The --> marker signals that output should begin here
```

```
# Llama 2 chat template
<s>[INST] <<SYS>>
{{ system_prompt }}
<</SYS>>

{{ user_message }} [/INST]

# Llama 3 chat template
<|begin_of_text|>
<|start_header_id|>system<|end_header_id|>
{{ system_prompt }}<|eot_id|>
<|start_header_id|>user<|end_header_id|>
{{ user_message }}<|eot_id|>
<|start_header_id|>assistant<|end_header_id|>
```

## Reference Tables

**Prompt format efficiency (same task, fewer tokens is better):**

| Prompt style | # tokens (GPT-4) |
|---|---|
| Verbose with labels (`Input:`, `Output:`) | 38 |
| Compact with arrow (`-->`) | 27 |

**CoT variations:**

| Variation | When to use |
|---|---|
| Zero-shot CoT ("think step by step") | Quick improvement, no examples available |
| Explicit CoT (list steps) | When you know the reasoning process |
| One-shot CoT (example with reasoning) | Domain-specific reasoning patterns |
| Self-critique | High-stakes tasks requiring validation |

## Anti-patterns

- **Using wrong chat template**: Silent failures — model still generates something reasonable but quality degrades. Always print final prompt before sending.
- **Giant single prompt for complex tasks**: Decompose instead; enables monitoring, debugging, parallelization.
- **Checking prompt tools blindly**: Tools generate hidden API calls (costs $$$); inspect produced prompts for typos and template errors.
- **Storing prompts in code**: Makes them hard to test, version, and share with non-engineers.
- **Ignoring context position**: Information in the middle of a long context is less likely to be used; put critical info at start/end.

## Key Takeaways

1. Exhaust prompt engineering before finetuning — it's faster and more portable.
2. CoT is the highest-value single prompt technique; "think step by step" works across models.
3. Decompose complex tasks into subtask prompts; chain them.
4. Use cheaper/weaker models for classification subtasks, stronger models for generation.
5. Version and separate prompts from code — treat them like first-class software artifacts.
6. Defend your application: validate inputs, monitor for extraction/injection, use output guardrails.

## Connects To

- **Ch 6**: Context construction (RAG and agents) extends prompt engineering with external information.
- **Ch 7**: Finetuning starts where prompt engineering reaches its ceiling.
- **Ch 10**: System prompt security and guardrails are part of production architecture.
