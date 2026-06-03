# Chapter 6: RAG and Agents

## Core Idea
Context construction — giving the model the right information for each query — is the single highest-leverage technique for improving output quality; RAG handles static knowledge and agents handle dynamic action-taking.

## Frameworks Introduced

- **RAG (Retrieval-Augmented Generation)**: Retrieve relevant context from external memory → augment prompt → generate response.
  - Retriever has two functions: indexing (prep data) and querying (fetch for each query).
  - RAG is "feature engineering for foundation models" — same purpose as feature engineering in classical ML.

- **Retrieval Algorithm Hierarchy**:
  1. Term-based (BM25, TF-IDF): fast, cheap, strong baseline; fails on synonyms.
  2. Embedding-based (vector search): semantic understanding; slower, more expensive, requires vector DB.
  3. Hybrid search: term-based for candidate retrieval → embedding-based reranking.
  - Start with BM25; only add vector search after term-based retrieval proves insufficient.

- **Reciprocal Rank Fusion (RRF)**: Combines rankings from multiple retrievers.
  - `Score(D) = Σ 1/(k + ri(D))` where k=60 (typical), ri = rank by retriever i.

- **Retrieval Optimization Tactics**:
  1. Chunking strategy (size, overlap, recursive splitting)
  2. Reranking (cheap retriever → expensive reranker)
  3. Query rewriting (reformulate ambiguous queries)
  4. Contextual retrieval (add context about document to each chunk before embedding)

- **Agent Pattern**: AI model as brain that plans a sequence of actions using tools to accomplish a task.
  - Tool inventory: knowledge augmentation (RAG, web search), capability extension (calculator, code interpreter), write actions (email, DB writes).
  - Planning: decouple plan generation from execution; validate plan before running it.

## Key Concepts

- **Chunking**: Splitting documents into manageable pieces for retrieval. Strategies: fixed-size, recursive (by section → paragraph → sentence), overlap to prevent context loss.
- **Chunk overlap**: Including shared tokens between consecutive chunks to prevent important context from being split.
- **Vector database**: Stores embeddings for similarity search; common expense: can be 1/5 to 1/2 of model API spending.
- **Approximate Nearest Neighbor (ANN)**: Efficient vector search algorithms (HNSW, LSH); trade accuracy for speed.
- **HNSW (Hierarchical Navigable Small World)**: High accuracy, fast query, slow to build; preferred for most RAG.
- **BM25**: Classic term-based retrieval algorithm; often better than vectors out-of-the-box.
- **Hybrid search**: Combining term-based and embedding-based retrieval.
- **Reranking**: Using a more expensive model to reorder the top candidates from the initial retriever.
- **Query rewriting**: Reformulating ambiguous queries (especially in multi-turn conversations) to be self-contained.
- **Contextual retrieval**: Adding surrounding context metadata to each chunk before embedding (Anthropic technique).
- **Function calling**: API feature that lets models invoke external tools; the mechanism enabling agents.
- **Compound mistake problem**: In agents, if accuracy per step is 95%, over 10 steps accuracy drops to ~60%, over 100 steps to ~0.6%.
- **Write actions**: Tools that modify the environment (send email, update DB, initiate transfer); high capability, high risk.
- **Tool inventory**: The set of tools an agent has access to; more tools = more capability but harder utilization.

## Mental Models

- RAG = feature engineering for LLMs; give the model the right information per query.
- Long context doesn't eliminate RAG: more context = more noise; RAG filters signal.
- Think of chunking like pagination: too small loses coherence, too large adds noise.
- Agents compound errors — use them for tasks where each step can be validated.
- Start agents with read-only actions before enabling write actions.

## Reference Tables

**Retrieval algorithm comparison:**

| | Term-based retrieval | Embedding-based retrieval |
|---|---|---|
| Querying speed | Much faster | Slower (embedding + vector search) |
| Performance baseline | Strong out-of-box | Better with finetuning |
| Term ambiguity | Fails on synonyms | Handles semantics |
| Cost | Much cheaper | Embedding + vector DB = expensive |

**RAG evaluation checklist:**
1. Evaluate retrieval quality (precision, recall of retrieved docs)
2. Evaluate final output quality (faithfulness to context, answer correctness)
3. Evaluate embedding quality (MTEB benchmark)

## Code Examples

```python
# Hybrid search: term-based first, then vector reranking
def hybrid_retrieve(query, top_k=10, rerank_k=3):
    # Step 1: Cheap term-based retrieval for candidates
    candidates = bm25_search(query, top_k=top_k)
    
    # Step 2: Expensive embedding-based reranking
    query_embedding = embed(query)
    scores = [(doc, cosine_sim(query_embedding, embed(doc))) 
              for doc in candidates]
    return sorted(scores, key=lambda x: x[1], reverse=True)[:rerank_k]

# Query rewriting for multi-turn conversations
QUERY_REWRITE_PROMPT = """Given the following conversation, rewrite the last 
user input to reflect what the user is actually asking.
Conversation: {conversation}
Rewritten query:"""
```

## Anti-patterns

- **Jumping straight to vector databases**: Start with BM25/term-based retrieval first — it often outperforms vectors out-of-the-box and is far cheaper.
- **Fixed chunk size without overlap**: Splits context at arbitrary boundaries; use recursive splitting with overlap.
- **Ignoring query rewriting in multi-turn conversations**: Ambiguous follow-up questions retrieve irrelevant context.
- **Giving agents write actions without validation**: Autonomous write actions require human-in-the-loop or strong validation before enabling.
- **RAG vs. long context as either/or**: RAG and long context are complementary; RAG filters signal, long context captures breadth.

## Key Takeaways

1. RAG is the highest-leverage technique for reducing hallucinations — always try it before finetuning for knowledge gaps.
2. Start with term-based retrieval (BM25), add vector search only when needed.
3. Chunking strategy significantly impacts RAG quality; always include overlap.
4. Agent accuracy degrades multiplicatively with number of steps; prefer simpler architectures.
5. "Finetuning is for form, RAG is for facts" — use RAG for knowledge, finetuning for output style/format.

## Connects To

- **Ch 5**: Prompt engineering provides instructions; RAG provides context.
- **Ch 7**: "RAG for facts, finetuning for form" decision framework.
- **Ch 9**: KV cache and prefill optimization affect RAG latency.
- **Ch 10**: RAG components appear in the progressive architecture build-up.
