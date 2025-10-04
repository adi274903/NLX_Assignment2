***

### Architecture Document  
**Assignment 2: Ground the Domain – From Naive RAG to Production Patterns**  
**Dataset:** RAG Mini Wikipedia  
**Models Used:** Llama 3.1 1B (LLM), Gemma 300M (embedding)  
**Vector Store:** ChromaDB (Google Drive mounted)  
**Enhancements:** Context window optimization + Reranking  
**Evaluation Framework:** ARES  

***

### 1. System Overview  
The Retrieval‑Augmented Generation (RAG) system developed for this assignment is structured into modular components prioritizing reproducibility, scalability, and ease of experimentation. The core design integrates Gemma 300M embeddings with a lightweight Llama 3.1 1B generation model. The pipeline is engineered to support both a naive baseline RAG (standard retrieval and generation) and an enhanced production-ready RAG with improved passage selection and reranking features.

The workflow comprises four primary modules:

1. **Document Processing and Embedding Generation**  
2. **Vector Indexing and Retrieval with ChromaDB**  
3. **Response Generation via Persona Prompting**  
4. **Automated Evaluation with ARES**

Each module is independently configurable, supporting extensible parameter sweeps and easy deployment adjustments aligned with research and production needs.

***

### 2. Data Pipeline and Preprocessing  
The system ingests the *RAG Mini Wikipedia* dataset, converting each entry into normalized passages with metadata. Documents are segmented into variable chunk sizes (256, 384, and 512 tokens) to empirically test effects on retrieval performance and context coverage. Initial experimentation revealed that while smaller chunks increase retrieval granularity, they may omit essential context for answer generation. Conversely, the 512-token configuration, paired with top‑k = 5 passage retrieval, offered consistently coherent and context-rich answers with minimal fragmentation. This configuration became the production default.

***

### 3. Embedding and Vector Index  
After preprocessing, each passage is embedded using **Gemma 300M**, outputting 300-dimensional vectors. Vectors, along with passage metadata (ID, title, token count), are stored in **ChromaDB**. Using ChromaDB (mounted through Google Drive) offers strong compatibility with Python, persistent cloud-backed storage, and flexible search schemas suitable for iterative experimentation.

ChromaDB is configured for efficient similarity search via cosine distance. Metadata enables rapid filtering and advanced reranking in later steps. This setup facilitates the reproducibility and transportability of the index across diverse compute environments, especially within Google Colab or similar platforms that connect to Google Drive for persistent storage.

***

### 4. Naive RAG Implementation  
The baseline RAG pipeline performs a straightforward retrieval-and-generate operation:

1. The query is embedded via Gemma 300M.
2. ChromaDB returns the top-k (typically 1 for baseline) candidate passages.
3. The retrieved passage is merged with the user query and input to Llama 3.1 1B for generation.

Multiple prompting styles were compared, with persona prompting (where the model assumes an informed domain expert persona) yielding the highest F1 (~8) and 0 match scores (due to the model answering).

***

### 5. Enhanced RAG Implementation  
To move beyond the naive pipeline, two advanced enhancements were developed:

1. **Context Window Optimization**  
   Passages are scored and trimmed through a custom windowing scheme, using cosine similarity and query density to retain only the most relevant segments, constrained to Llama 3.1 1B’s context window. This improves faithfulness by excluding extraneous or semantically diluted material from prompts.

2. **Reranking with MiniLM**  
   Following initial retrieval (top‑5) from ChromaDB, passages are reranked using a MiniLM-based cross-encoder for fine-grained semantic matching. The most relevant top-3 passages are selected for aggregation in the input to the LLM. This two-stage retrieval process raises overall retrieval precision and reduces hallucinations.

Configuration files (e.g., `config.yaml`) allow runtime adjustments of chunk size, top‑k, and reranking thresholds to support repeatable, parameterized experimentation.

***

### 6. Evaluation Workflow and Metrics  
ARES is used for evaluation, measuring faithfulness, context precision, recall, and answer helpfulness for given Enhanced RAG (optimized + reranked) architecture

Over 180 diverse test queries, metrics are aggregated and logged for statistical robustness. The enhanced system demonstrated clear gains in faithfulness and context recall, supporting the efficacy of fine-tuned passage selection and reranking.

***

### 7. System Design Principles and Justifications  
**Model and Vector Store Selection:** Llama 3.1 1B and Gemma 300M provide a balance of cost, speed, and generative quality suitable for resource-constrained and portable deployments. ChromaDB mounted through Google Drive enables persistent, cloud-synced storage and rapid vector operations within Colab and local workflows.

**Chunk Size Rationale:** 512-token chunks with top‑k = 5 maximized recall and answer coherency, balancing retrieval volume with manageable input length.

**Reproducibility and Modularity:** Jupyter notebooks and modular source files (`src/naive_rag.ipynb`, `src/enhanced_rag.ipynb`, `src/evaluation.ipynb`, `src/data_exploration.ipynb`) facilitate progressive experimentation and expansion to frameworks like RAGAs. Flexible configuration and fallback design (ChromaDB/FAISS/Milvus) support rapid deployment or platform changes.

**Logging and Error Management:** Comprehensive logging and fallback support ensure transparency and simplify post-hoc debugging and performance analysis.

***

### 8. Conclusion  
This architecture offers a robust, modern retrieval-augmented pipeline tailored for small-to-medium datasets and limited compute. Using persistent ChromaDB storage, together with Llama 3.1 1B and MiniLM for reranking, the system supports practical, repeatable research and production extension. Experiments with persona prompting and 512-token top‑k = 5 retrieval validate that context-aware passage selection is key to real-world RAG quality. Automated, evidence-driven evaluation via ARES affirms the deployment readiness and scientific rigor of this system.
