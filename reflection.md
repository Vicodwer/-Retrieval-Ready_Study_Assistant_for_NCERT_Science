# Reflection Questionnaire — Wk10 Study Assistant v2.0

## Part A — Implementation Specifics

### A1. Chunking decisions, with evidence
**Parameters:**
- Tokenizer: cl100k_base
- Max tokens: 250
- Split strategy: paragraph first, sentence fallback
- Content-type rules: regex on heading/example markers

**Three specific chunks:**

1. chunk_0000 — content_type: prose
   First page intro text. No example/activity keywords found.
   Correctly classified as prose.

2. chunk_0005 — content_type: worked_example
   Contains "10.1.1 Tuning fork" — matched pattern "example" in heading.
   Page 3, 101 tokens.

3. chunk_0002 — content_type: question_or_exercise
   Contains "Activity 10.1: Let us explore" — matched "activity\s+\d+"
   Page 2, 241 tokens.

### A2. The chunk that surprised me
chunk_0032 (page 12, 231 tokens):
"Sound Waves: Characteristics and Applications 195 Density Distance
Amplitude..."
Expected: prose. Got: worked_example.
The heuristic matched "example" in surrounding figure text.
This is a false positive — the chunk is actually a figure description,
not a worked example. The classifier was fooled by the word "example"
appearing in the figure caption context.

### A3. Loader choice
Stayed with PyMuPDF from Wk9. Attempted OpenDataLoader-PDF but it
required Java which was not installed. PyMuPDF handled the Sound Waves
chapter cleanly — paragraphs extracted correctly, page numbers preserved.
One observed difference: PyMuPDF rendered some figure labels as garbled
symbols (λλλλ in chunk_0031). OpenDataLoader-PDF would likely handle
these better with its structure-aware parsing.

## Part B — Numbers from Evaluation

### B1. Eval scores, raw
- Correct: 8/9 in-scope questions (v1), 9/9 (v2)
- Grounded: 8/9 in-scope questions (v1), 9/9 (v2)
- OOS Refused: 3/3 both versions

The number that bothered me most was 8/9 grounded in v1.
q05 failed because the model said "I don't have that" when
the answer WAS in the corpus — just spread across two chunks.
That is a retrieval+synthesis failure, not a model failure.

### B2. The single worst question
**Question:** What is the difference between loudness and pitch?
**Answer given (v1):** "The provided text does not contain any
information regarding the difference between loudness and pitch."
**Top-3 chunk_ids:** chunk_0031, chunk_0044, chunk_0045

**Failure mode:** Ambiguous query (§3 catalog).
The corpus explains loudness and pitch as separate concepts but never
directly compares them. The model could not synthesize a comparison
from chunks that describe each concept independently.

### B3. Before-and-after on fix
**Fix:** Switched generation model from gemma:7b to gemma2:2b.
Also implemented query decomposition for "difference between" queries.

**Delta on q05:**
- Before: "I don't have that in my study materials" (WRONG)
- After: "Loudness is a perception of sound intensity, while pitch
  refers to the frequency of sound waves." (CORRECT) 

**Delta across full 12-Q set:**
- Correct: 8/9 → 9/9 (+1)
- Grounded: 8/9 → 9/9 (+1)
- OOS Refused: 3/3 → 3/3 (unchanged)

## Part C — The 30-Second Debugging Story

### C1. The retrieved chunk that fooled me
**Query:** What is the frequency of sound?
**Top-1 chunk:** chunk_0031 (score: 0.9057)
**Chunk text:** "10.18 indicating that it is almost a single
frequency sound. λ λ λ λ"

This ranked top-1 because the query and chunk both contain the word
"frequency" — but the chunk is a garbled figure caption with no
actual definition of frequency. BM25 would have ranked this lower
because the meaningful content is mostly symbols. Dense embeddings
matched on "frequency" semantically without seeing that the chunk
was mostly noise.

### C2. The bug that took longest
The Chroma telemetry error on startup:
"Failed to send telemetry event ClientStartEvent"
Took 30 minutes to resolve. First tried disabling telemetry with
chromadb.telemetry.product.posthog.Posthog = None — this broke
the client entirely with a TypeError.
Actual fix: restart kernel and use plain PersistentClient without
any telemetry overrides. The warnings are harmless — Chroma works
fine despite them.
Fastest path for teammates: ignore the red warnings, check that
the 3 success lines print below them.

### C3. The thing that still bothers me
chunk_0031 keeps appearing as top-1 or top-2 for many unrelated
queries. It contains garbled figure symbols (λλλλ) and very little
real content, but its embedding is somehow similar to many queries.
I would filter chunks with high special-character ratios (>5%) at
retrieval time in Wk11.

## Part D — Architecture and Tradeoffs

### D1. Why hybrid retrieval (or why not)?
In this project I used dense-only retrieval. The case for hybrid:
Query "g = 9.8 m/s²" — dense embeddings would struggle with the
exact numeric token while BM25 finds it trivially.
In my eval, query q01 "What is the speed of sound in air?" returned
chunk_0064 (score 0.90) which contained "331 m s–1" — dense worked
here because the semantic meaning matched. But query q04 about SONAR
returned chunk_0058 which mentioned SONAR in passing rather than
defining it — BM25 on the exact term "SONAR" would have ranked the
definition chunk higher.

### D2. The CRAG / Self-RAG question
CRAG would have helped with q05 in v1. The grader would have seen
that the retrieved chunks had low relevance to "difference between"
and triggered a query rewrite. However for this 24-page corpus,
CRAG is overkill — the corpus is small enough that increasing k
and improving the prompt handles most failures. CRAG becomes worth
building when the corpus is large enough that bad retrievals are
frequent and expensive to debug manually.

### D3. Honest pilot readiness
Honest answer: not ready for 100 students yet.
Three things to verify first:
1. chunk_0031 garbled chunk — appears in top-k for too many queries,
   polluting context. Filter it before deploying.
   (eval_scored.csv rows q04, q07, q09 all retrieved it)
2. Synthesis queries — the corpus never directly compares concepts.
   Students will ask comparison questions. Need synthesis prompt.
3. Latency — gemma2:2b takes 15-30 seconds per query on CPU.
   100 concurrent students would queue for minutes. Need API-based
   generation or GPU inference for production.

## Part E — Effort and Self-Assessment

### E1. Effort rating
8/10. Built the entire pipeline from scratch in one day including
debugging environment issues (Java missing, Chroma telemetry,
venv/Jupyter mismatch). Genuinely proud of diagnosing the
chunk_0031 noise problem and the model-switch fix for q05.

### E2. The gap between me and a stronger student
A stronger student probably implemented BM25 hybrid retrieval
alongside dense. I used dense-only. The reason: time constraint
and environment debugging ate the hybrid retrieval time slot.
The value is clear — q04 SONAR and q07 speed paraphrased both
would have benefited from exact-term matching.

### E3. The Industry Pointer I'd explore in 6 months
**Eval-driven development** (§9).
My Wk10 eval set has 12 questions — too small to catch edge cases
in production. Real teams version their eval sets and add a question
every time a production failure is found. The first step would be
setting up a simple CSV-based eval runner that runs automatically
on every retrieval-touching code change, so regressions are caught
before they reach students.

### E4. Two more days
**First thing:** Fix chunk_0031 — add a special character filter
at retrieval to stop this garbled chunk from polluting every query.
This is the single highest-leverage fix based on eval data.

**Last thing:** Add BM25 hybrid retrieval alongside dense.
This is high value but lower urgency than the chunk fix —
the dense-only system already scores 9/9. Hybrid would help
on edge cases not yet in the eval set.
