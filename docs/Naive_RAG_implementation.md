***

### Naive RAG Implementation Document

**Overview**  
The Naive Retrieval-Augmented Generation (RAG) system was designed as a modular and reproducible pipeline to test the fundamentals of query-based context retrieval on the Mini Wikipedia dataset. The core implementation leverages Gemma 300M for embedding generation, ChromaDB as the vector store (mounted to Google Drive for persistence), and Llama 3.1 1B as the language model responsible for final answer generation.

**Data Processing**  
The dataset was preprocessed by segmenting Wikipedia passages into chunks of a fixed token length. Three chunk sizes—256, 384, and 512 tokens—were evaluated. Tokenization ensures that each passage contains whole sentences and preserves semantic boundaries.

After preprocessing, each chunk was embedded using Gemma 300M. Each passage’s embedding (a 300-dimensional vector) was stored in ChromaDB along with metadata containing the original document title, token length, and positional information. ChromaDB’s persistent backing through Google Drive allowed for reproducible experiments across platforms.

**Retrieval Pipeline**  
Upon receiving a user query, the query is tokenized and embedded using the same Gemma 300M model as for the corpus. A k-nearest neighbor search is performed in ChromaDB, returning the top_k most similar document embeddings. The following top_k values were tested: 1, 3, and 5.

Retrieved passages are joined with the original user query according to a fixed prompt template, then passed as input to Llama 3.1 1B. No further document filtering or reranking is performed at this stage, making the pipeline “naive” in its retrieval and aggregation logic.

**Prompting Strategies**  
Evaluation was performed across three prompting strategies: persona, chain-of-thought (CoT), and instruction; each strategy aimed to optimize model outputs under different context settings. Persona prompting was consistently found to yield higher F1 scores by explicitly instructing the model to act as a domain expert.

**Implementation Code Structure**  
All Naive RAG logic is organized in separate, well-documented scripts and notebooks:
- `src/naive_rag.ipynb` contains the primary routines for processing, embedding, storing, and retrieving passages.
- `config.json` is available in the main github repository along with `requirements.txt` which can be used for installation of necessary packages and files.
- All critical parameters—like chunk size, top_k, or embedding model—are  `config.json`  through a global config block, simplifying experimentation.
- .env is not given as it is a secure key made by the author, usage of your own API key is recommended for Hugging Face access.

Full error handling and logging have been included to catch index errors, handle empty retrieval results, and trace performance metrics. Retrieval latency, returned passage IDs, and prompt lengths are logged for each query.

**Configuration and Results**  
Empirical results highlight key trade-offs:
- **Chunk size 256, top_k 3:** Persona F1 = 7.05; CoT F1 = 1.75; Instruct F1 = 1.95.
- **Chunk size 512, top_k 5:** Persona F1 = 8.04; CoT F1 = 1.88; Instruct F1 = 2.21.
- **Chunk size 512, top_k 1:** Persona F1 = 5.23; CoT F1 = 1.66; Instruct F1 = 1.92.

Across all trials, exact match was zero, reflecting the inherent difficulty of n-gram based strict evaluation on open-domain generative models at small scale.

**Conclusions**  
Naive RAG performance is significantly improved by increasing both chunk size and retrieval breadth (top_k). Larger chunks offer richer context, while higher top_k values increase the likelihood of relevant document inclusion, which the generative model can leverage for better answers. This naive baseline serves as a strong foundation for evaluating advanced retrieval and ranking strategies in subsequent iterations.
