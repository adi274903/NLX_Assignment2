# NLX_Assignment2
The following repository is for the CMU course Applications of NLX and LLM. It is especially for Assignment 2.
**** 
## Directory Structure
****
```
NLX_Assignment2/
├── data/
│   ├── evaluation/
│   │   ├── ERAG_persona_results_181.csv       ## Evaluation Data of Enhanced RAG
│   │   ├── NRAG_persona_results_181.csv       ## Naive RAG evaluation data
│   │   └── evaluation_readme.md
│   ├── processed/
│   │   ├── dataset.md                         ## Null markdown
│   │   ├── test.csv
│   │   └── train.csv
├── docs/
│   ├── Architecture_Document.md          
│   ├── Enhanced_RAG_implementation.md
│   ├── Evaluation Results.pdf
│   ├── Evaluation_Results.md                  ## Can also be considered the comparison_results as no ipynb or csv results for the same
│   ├── Naive_RAG_implementation.md
│   └── Technical Report.pdf                   ## Contains brief Appendix
├── notebooks/src/                             
│   ├── Advanced_RAG_Final.ipynb
│   ├── Evaluation.ipynb
│   ├── Naive_RAG.ipynb
│   └── data_exploration.ipynb
├── results/                                  
│   ├── naive_rag/
│   │   ├── predictions_256_3_prompts.csv
│   │   ├── predictions_512_1_prompts.csv
│   │   ├── predictions_512_5_prompts.csv
│   │   └── results.md
│   ├── ERAG_evaluation_results_with_scores.csv
│   └── NRAG_evaluation_results_with_scores.csv
├── config.json
├── requirements.txt
├── README.md

```

****

## AI Usage Log/ Attribution

- **Tool**: Perplexity Sonar
  
- **Purpose**: Code Debugging and Optimization for A100 GPU and for OpenAI RAGAs implementation
  
- **Input**: Help me debug/ implement code for the XX task
  
- **Output Usage**: Utilized in most of the notebooks where debugging was becoming problematic, especially RAGAs

- **Verification**: By implementation and debugging accuracy was confirmed, but documentation needed to be checked
