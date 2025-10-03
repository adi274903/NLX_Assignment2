Following is the Explanation of Results for the Naive RAG:

- For the Naive RAG, when chunk was 256 and top_k was 3, scores were as follows:

  Persona
  F1: 7.0474991185064
  Exact Match: 0.0
  
  CoT
  F1: 1.7489099313033336
  Exact Match: 0.0
  
  Instruct
  F1: 1.9454996044690216
  Exact Match: 0.0


- For the Naive RAG, when chunk was 512 and top_k was 5, scores were as follows:
  
  Persona
  F1: 8.035665896184275
  Exact Match: 0.0

  CoT
  F1: 1.880166585135581
  Exact Match: 0.0

  Instruct
  F1: 2.212115710955511
  Exact Match: 0.0

- For the Naive RAG, when chunk was 512 and top_k was 1, scores were as follows:

  Persona
  F1: 5.228847065911152
  Exact Match: 0.0

  CoT
  F1: 1.6558436316721852
  Exact Match: 0.0

  Instruct
  F1: 1.922326958600702
  Exact Match: 0.0

From the Given we can conclude that a higher top_k as well as a higher chunk size is better for RAGs
