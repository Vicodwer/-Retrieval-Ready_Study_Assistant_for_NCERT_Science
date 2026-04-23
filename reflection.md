1. What I Learned

This project helped me understand how a Retrieval-Augmented Generation (RAG) system works in practice. I learned that building such a system is not just about using a language model, but about designing the entire pipeline carefully—especially text cleaning, chunking, and retrieval.

One key learning was that retrieval quality is more important than model size. Even with a small local model (Gemma), the system produced good answers when the correct chunk was retrieved.

2. Challenges Faced

I faced several challenges during the project:

Text cleaning issues: The extracted textbook content contained noise like activities and headings, which affected retrieval quality.
Chunking problems: Some important information was split across chunks, leading to retrieval failures.
BM25 limitations: The retriever sometimes returned partially relevant chunks instead of the best one.
Model refusals: The model occasionally refused to answer even when the answer was indirectly present.

3. How I Solved Them

Improved text cleaning by removing irrelevant sections
Adjusted chunking strategy and tested different sizes
Switched to top-1 retrieval for more focused context
Improved the prompt to enforce grounding
Added a guardrail to detect out-of-scope questions

4. Key Insights

Better chunking improves retrieval more than changing the model
More context is not always better (n=1 worked better than n=3)
Prompt design strongly affects answer quality
Grounding prevents hallucination effectively
Small models can work well with good retrieval

5. What I Would Improve Next

If I had more time, I would:

Use hybrid retrieval (BM25 + embeddings) for better matching
Improve chunking using sentence-based splitting
Use a slightly stronger model for better reasoning
Add a simple user interface (Streamlit) for interaction

6. Final Thought

This project showed me that building reliable AI systems requires careful engineering decisions. The most important takeaway is that retrieval and data quality matter more than just using a powerful model.