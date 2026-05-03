# Study Assistant v2.0 — PariShiksha
**Wk10 Mini-Project | PG Diploma AI-ML & Agentic AI | IIT Gandhinagar**

## What this is
A RAG-based study assistant for NCERT Class 9 Science — Sound Waves chapter.
Built for PariShiksha's pilot to help students get grounded, cited answers
from the textbook — not hallucinated responses.

## Setup

### Requirements
- Python 3.11
- Ollama with gemma2:2b installed

### Install
```bash
git clone https://github.com/Vicodwer/Retrieval-Ready_Study_Assistant_for_NCERT_Science.git
cd Retrieval-Ready_Study_Assistant_for_NCERT_Science
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

### Add PDF
Download NCERT Sound Waves chapter and place at:
data/iesc110.pdf
Source: https://ncert.nic.in/textbook.php?iesc1=0-11

### Run Ollama
```bash
ollama pull gemma2:2b
ollama serve
```

### Run Notebook
```bash
jupyter notebook
```
Open notebook.ipynb and run all cells in order.

## Architecture
- **PDF Loading**: PyMuPDF
- **Chunking**: Content-type-aware (prose/worked_example/question_or_exercise), max 250 tokens
- **Embeddings**: BAAI/bge-small-en (local, free, 384 dimensions)
- **Vector Store**: Chroma (persistent, cosine similarity)
- **Generation**: Ollama gemma2:2b (local, free)
- **Eval**: 12-question set scored on 3 axes

## Results
| Metric | v1 | v2 |
|--------|----|----|
| Correct | 8/9 | 9/9 |
| Grounded | 8/9 | 9/9 |
| OOS Refused | 3/3 | 3/3 |

## Deliverables
| File | Description |
|------|-------------|
| wk10_chunks.json | 69 chunks with metadata |
| retrieval_log.json | 10-query retrieval log |
| retrieval_misses.md | 3 miss diagnoses |
| prompt_diff.md | Permissive vs strict prompt comparison |
| eval_scored.csv | 12-Q manual scores (v1) |
| eval_v2_scored.csv | 12-Q manual scores (v2) |
| fix_memo.md | Targeted fix analysis |
| reflection.md | Full reflection questionnaire |

## What You Can Learn From This Project

### 1. RAG Pipeline from Scratch
How to build a complete Retrieval-Augmented Generation pipeline:
PDF → Chunks → Embeddings → Vector Store → Retrieval → Generation.
Every step is visible and debuggable — no black boxes.

### 2. Chunking Strategy Matters More Than You Think
The gap between naive chunking and content-type-aware chunking is large.
Splitting a worked example mid-sentence destroys retrieval quality.
Token-aware sizing (using tiktoken) beats character-count sizing.

### 3. Dense Embeddings vs Keyword Search
Local embeddings (bge-small-en) find semantically similar chunks
even when the exact words don't match. But they fail on exact
identifiers, formulas, and numeric tokens — where BM25 wins.

### 4. Strict Prompting is Not Optional
A permissive prompt ("answer using this context") relies on the
model's judgment for refusal. A strict prompt ("if not in context,
say exactly X") makes refusal a hard constraint.
The difference: ~30% failure rate vs ~5% failure rate on
out-of-scope queries.

### 5. Always Print Retrieved Chunks Before Blaming the Model
When an answer is wrong, the bug is almost never the LLM.
Print top-k retrieved chunks first. 9 out of 10 times the
problem is retrieval, not generation.

### 6. Evaluation Drives Everything
Without an honest eval set you cannot tell if your changes helped.
Include:
- Direct questions (easy)
- Paraphrased questions (tests semantic retrieval)
- Out-of-scope questions (tests refusal)
- Plausibly answerable OOS (the hardest test)

### 7. Model Size is Not Everything
gemma2:2b (smaller) outperformed gemma:7b (larger) on synthesis
queries in this project. Faster, cheaper, better on this task.
Always benchmark before assuming bigger = better.

### 8. Single Variable Iteration
Change one thing at a time. Commit after each change.
Your git log becomes your experiment log.
This is how real ML engineers debug — not by changing everything at once.

### 9. Local-First Development is Viable
Entire pipeline runs with zero API costs:
- PyMuPDF for PDF extraction (free)
- bge-small-en for embeddings (free, local)
- Chroma for vector store (free, local)
- Ollama + gemma2:2b for generation (free, local)

### 10. Production Readiness Checklist
Before shipping a RAG system ask:
- Does it refuse gracefully when the answer is not in the corpus?
- Does every answer cite its source chunk?
- Have you tested paraphrased versions of your eval questions?
- Is your chunker preserving worked examples and tables intact?
- Can a fresh clone reproduce your results end-to-end?

