***

## Automated Evaluation Report: Naive vs Enhanced RAG

**Framework:** RAGAS  
**Judge:** OpenAI GPT-4o Mini  
**Metrics:** Correctness, Faithfulness, Helpfulness, Perfect Answer Rate

***

### Methodology

Both naive and enhanced RAG pipelines were evaluated over 180 diverse questions representative of broad open-domain knowledge. Each system’s generated responses were assessed using RAGAS, employing a large language model judge to score each answer against three industry-standard criteria:
- **Correctness:** Is the output factually aligned with ground truth?
- **Faithfulness:** Does the answer remain grounded in the retrieved evidence, avoiding unsupported statements?
- **Helpfulness:** Does the response meaningfully fulfill the query’s intent?

Each score is returned using structured, qualitative categories. For aggregate reporting, performance percentages are computed and the “Perfect” metric indicates where all three criteria are simultaneously met.

***

### Aggregated Results Table

| Metric         | Naive RAG | Enhanced RAG |
|----------------|-----------|-------------|
| Total Evaluated|   180     |   180       |
| Errors         |     0     |     0       |
| Correct        |  74 (41.1%) | 113 (62.8%)|
| Faithful       |  80 (44.4%) | 120 (66.7%)|
| Helpful        |  78 (43.3%) | 118 (65.6%)|
| Perfect (All)  |  72 (40.0%) | 110 (61.1%)|

***

### Results Interpretation

- **Correctness:** The enhanced RAG produced correct answers for 62.8% of questions compared to just 41.1% for the naive baseline, showing a significant absolute improvement of over 20%.
- **Faithfulness:** Context-grounding improved substantially, with 66.7% of enhanced answers being directly supported by retrieved evidence, versus 44.4% for the naive pipeline.
- **Helpfulness:** 65.6% of answers from the enhanced system were marked helpful, against 43.3% for the naive approach, demonstrating better alignment with user intent and practical requirements.
- **Perfect Answers:** Most notably, 61.1% of enhanced system answers met all three criteria, compared to only 40.0% in the naive case.

No errors or broken responses were returned in either setup, reflecting high reliability of both implementations and the automated judge.

***

### Comparison and Analysis

These results demonstrate substantial gains across all key dimensions by introducing advanced features such as context window optimization and reranking. The enhanced RAG pipeline’s ability to deliver contextually faithful, practically useful, and correct answers is markedly higher. The “Perfect Answer” rate—where correctness, faithfulness, and helpfulness all coincide—is a decisive, holistic indicator of quality and shows an improvement of over 20 percentage points.

Areas for further enhancement remain, as nearly 40% of questions still lack fully correct, context-faithful, and helpful answers even in the advanced system. Focused tuning of retrieval, prompt engineering or further post-hoc reranking may yield continued gains.

***

### Conclusion

The evaluation highlights the transformative effect of advanced retrieval and reranking on RAG performance. By systematically measuring correctness, faithfulness, and helpfulness using an LLM judge, the enhanced pipeline delivers much higher quality answers, justifying its complexity for real-world QA deployments. These findings lay the groundwork for targeted iterative improvements, demonstrating the necessity of structured RAG evaluation in AI system development.
