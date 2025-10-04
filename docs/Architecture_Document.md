
### Architecture Document  
**Assignment 2: Ground the Domain – From Naive RAG to Production Patterns**  
**Dataset:** RAG Mini Wikipedia  
**Models Used:** Llama 3.1 1B (LLM), Gemma 300M (embedding)  
**Enhancements:** Context window optimization + Reranking  
**Evaluation Framework:** ARES  

***

### 1. System Overview  
The Retrieval‑Augmented Generation (RAG) system developed for this assignment is structured into modular components for reproducibility and scalability. The architecture integrates a lightweight open‑weight embedding generator (Gemma 300M) with a compact generation model (Llama 3.1 1B). The pipeline bridges document retrieval, context construction, and generation to produce responses grounded in external knowledge. The goal is to contrast a naïve RAG pipeline against an enhanced version equipped with advanced retrieval and ranking features.

The overall workflow follows four sequential modules:

1. **Document Processing and Embedding Generation**  
2. **Retrieval and Candidate Context Construction**  
3. **Response Generation via Persona Prompting**  
4. **Output Evaluation with ARES**

This structure promotes modular experimentation, enabling systematic tuning of parameters and the addition of production‑grade enhancements without redesigning the underlying codebase.

***

### 2. Data Pipeline and Preprocessing  
The input corpus originates from the *RAG Mini Wikipedia* dataset, consisting of semantically diverse text segments suitable for small‑scale experimentation. Each entry contains a title, passage, and metadata. Prior to embedding, documents are tokenized, normalized, and segmented into variable chunk sizes: 256, 384, and 512 tokens.  

Empirical exploration during Step 4 showed that chunk size directly influenced retrieval relevance and generation quality. Shorter segments (256) increased granularity but fragmented context, occasionally missing semantic continuity. Medium‑length (384) balanced coverage and density, whereas 512‑token chunks consistently yielded the most coherent and contextually grounded answers during evaluation—especially when combined with *top‑k = 5* retrieval. Thus, 512 was selected as the final chunk setting for the production configuration.

***

### 3. Embedding and Vector Index  
All text chunks are embedded using **Gemma 300M**, a compact transformer optimized for semantically dense sentence embeddings. The model encodes each chunk into a 300‑dimensional vector representation. Dimensionality trade‑offs were analyzed to ensure computational economy on CPU/GPU‑free environments while preserving semantic fidelity.

The embedding vectors are indexed using **FAISS**, chosen for its efficient approximate nearest‑neighbor (ANN) search and Python compatibility. The index is built using L2 distance metrics with flat indexing for reproducibility. Each embedding is associated with metadata (document ID, section title, token length) stored in JSON format. This configuration allows filtering or reranking by additional attributes in later stages.

***

### 4. Naive RAG Implementation  
The baseline (naïve) RAG pipeline integrates the embedding index with the **Llama 3.1 1B** inference model through a simple retrieval‑and‑generate loop. For each query:  
1. The query is converted into an embedding using Gemma 300M.  
2. The top‑k (= 1) document vectors are retrieved from FAISS.  
3. The full retrieved text is concatenated with the query and passed to Llama 3.1 1B for response generation.

Error handling ensures fallback retrieval if the index search returns empty results. Logging captures query latency, document IDs retrieved, and token utilization.  

This naïve configuration served as the foundation for evaluation using multiple prompting styles: chain‑of‑thought, instruction, and persona prompting. Among these, **persona prompting**—which prompts the model to assume an informed “domain expert” persona—produced the highest F1 and Exact Match scores, emphasizing the importance of context framing for smaller LLMs.

***

### 5. Enhanced RAG Implementation  
After establishing the baseline, two advanced features were added to improve contextual precision and reduce hallucination:

1. **Context Window Optimization**  
   Rather than feeding all retrieved passages verbatim, an adaptive windowing technique selectively trims irrelevant or redundant segments before model input. Each candidate passage is scored for query‑term density and semantic overlap using cosine similarity. Only the top sections that fit the available Llama 3.1 1B context length are concatenated. This reduces prompt dilution, improving faithfulness and factual coherence.

2. **Reranking with MiniLM**  
   A reranking model (based on MiniLM) is employed as a second‑stage filter. After initial retrieval (top‑5 from FAISS), each candidate document is reranked by semantic relevance using a MiniLM‑based cross‑encoder. The top‑3 passages are re‑ordered and merged according to descending relevance probability. This approach improved retrieval precision and contextual grounding without substantially increasing computational cost.

The enhanced pipeline retains the same overall structure but substitutes the naïve retrieval mechanism with a two‑stage retrieval and window optimization. Configuration files under `config.yaml` allow adjustable parameters (chunk size, top‑k, and reranker thresholds) to sustain reproducibility.

***

### 6. Evaluation Workflow and Metrics  
Evaluation is executed through the **ARES** framework, providing automated metrics such as *faithfulness*, *context precision*, *context recall*, and *answer helpfulness*. The comparison uses three evaluation cohorts:

- Naïve RAG (top‑1)
- Naïve RAG (top‑5)
- Enhanced RAG (reranked + optimized)

ARES runs over 100 test queries drawn from multiple domains within the Mini Wikipedia dataset. Output metrics are aggregated into CSV logs for further analysis.  

Preliminary insights demonstrated measurable gains in faithfulness and context recall in the enhanced setup, consistent with the design hypothesis that refined context selection and reranking improve factual grounding. Context precision increased particularly for subjective or multi‑entity questions, where the baseline often produced hallucinated or incomplete responses.

***

### 7. System Design Principles and Justifications  
**Model Selection:** Llama 3.1 1B was chosen for its efficiency and interpretability on limited GPU/Colab environments. The small embedding model Gemma 300M complements this choice by offering fast inference and sufficient representational power for similarity search.

**Chunk Length Justification:** The 512‑token setting was preferred for its superior answer coherence and minimal context fragmentation under multi‑paragraph queries. Combined with top‑k = 5, it yielded balanced recall and manageable prompt length.

**Scalability and Production Readiness:**  
- Modular file organization (`src/naive_rag.py`, `src/enhanced_rag.py`, `evaluation.py`) ensures ease of extension toward other evaluation frameworks such as RAGAs.  
- Fallback mechanisms allow the system to switch between FAISS and Milvus without major reimplementation.
- Configuration‑driven design supports scaling to cloud deployment or integration with orchestration tools.

**Logging and Error Management:** All major components include structured logging for retrieved items, inference time per query, and API response codes, enabling transparent debugging and performance analysis.

***

### 8. Conclusion  
The architecture successfully transitions from a naïve retrieval‑generate setup to an enhanced, production‑ready RAG pipeline emphasizing efficient retrieval refinement and context management. The combination of Llama 3.1 1B, Gemma 300M, and MiniLM strikes an effective balance between performance and computational cost. Empirical findings validate that persona prompting and 512‑token chunks with top‑k = 5 retrieval deliver optimal results for this deployment scale. With automated evaluation through ARES, the system aligns with real‑world development practices for evidence‑based model improvement and reproducible experimentation.
