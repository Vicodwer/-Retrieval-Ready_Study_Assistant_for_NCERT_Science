# -Retrieval-Ready_Study_Assistant_for_NCERT_Science


##  Overview

This project implements a **Retrieval-Augmented Generation (RAG)** system to answer Grade 8 Science questions from a textbook chapter on **atomic structure**.

The system retrieves relevant content using **BM25** and generates **grounded answers** using a local language model (**Gemma via Ollama**). It is designed to **avoid hallucinations** and only answer from the provided chapter.

---

##  Objectives

* Build a reliable question-answering system using textbook data
* Ensure answers are **factually grounded**
* Implement **retrieval + generation pipeline**
* Evaluate system performance and analyze failures

---

##  System Architecture

### 1. Text Processing

* Extracted and cleaned textbook content
* Removed noise (activities, headings, extra content)

### 2. Chunking

* Split text into manageable chunks
* Experimented with **small vs large chunks**

### 3. Retrieval (BM25)

* Used **BM25 algorithm** for keyword-based search
* Retrieved top relevant chunks
* Final system uses **top-1 retrieval (n=1)** for better precision

### 4. Answer Generation

* Used **Gemma (local LLM via Ollama)**
* Prompt enforces:

  * Use only retrieved context
  * No external knowledge
  * Refuse if answer not present

---

##  Evaluation

The system was tested on:

* Direct textbook questions
* Conceptual questions
* Out-of-scope queries

### Metrics:

* ✔ Correctness
* ✔ Groundedness
* ✔ Refusal Appropriateness

Results are stored in:

```
evaluation_results.csv
```

---

##  Failure Analysis

Key issues observed:

* Retrieval mismatch (wrong chunk selected)
* Chunk noise (extra irrelevant information)
* Implicit answers not detected
* Small model limitations

Detailed analysis in:

```
failure_modes.md
```

---

## Experiments (Stretch)

###  Chunk Size Experiment

* Compared small vs large chunks
* Smaller chunks → more precise retrieval
* Larger chunks → more noise

###  Prompt Improvement

* Improved prompt produced:

  * clearer answers
  * better grounding
  * fewer hallucinations

---

##  Advanced Improvements

### Guardrail

* Added keyword-based scope detection
* Rejects unrelated queries before processing

Example:

```
Q: Who is the Prime Minister of India?
A: This question is outside the scope of the chapter.
```

---

##  Bonus Features

* Display retrieved context (explainability)
* Compared `n=1` vs `n=3` retrieval
* Tested out-of-scope refusal behavior

---

##  Tech Stack

* Python
* BM25 (`rank-bm25`)
* Ollama (local LLM)
* Gemma model
* Pandas
* Jupyter Notebook

---

##  Setup Instructions

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Install Ollama

Download from: https://ollama.com

### 3. Pull model

```bash
ollama pull gemma:2b
```

### 4. Run notebook

Open:

```
notebook.ipynb
```

Run all cells.

---

##  Project Structure

```
 project-folder
│── notebook.ipynb
│── evaluation_results.csv
│── failure_modes.md
│── README.md
│── requirements.txt
```

---

##  Key Insights

* Retrieval quality matters more than model size
* Smaller, focused context improves answers
* Prompt design significantly impacts results
* Grounding prevents hallucination effectively

---

##  Future Improvements

* Hybrid retrieval (BM25 + embeddings)
* Better chunking (sentence-based)
* Stronger local model
* Simple UI (Streamlit app)

---

##  Conclusion

This project demonstrates a complete RAG pipeline with retrieval, generation, evaluation, and improvement cycles. It highlights that **engineering decisions like chunking, retrieval, and prompting are more important than just using a powerful model**.

---

##  Author

**Vishal Pagadala**

---
