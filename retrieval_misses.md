# Retrieval Misses Analysis

## Query 3: "How do humans hear sound?"
- **Top-1 chunk_id**: chunk_0000
- **Score**: 0.8725
- **Wrong chunk preview**: Chapter intro page — general overview of sound
- **Diagnosis**: Embedding miss — the query contains "humans hear" which 
  matched the intro page's general mention of everyday sounds. 
  The actual answer about auditory range is in chunk_0044 (rank 3).
  This is a **retrieval ranking failure** — right chunk exists but ranked 3rd.

## Query 9: "What is SONAR?"
- **Top-1 chunk_id**: chunk_0058  
- **Score**: 0.8744
- **Wrong chunk preview**: General sonar technology paragraph, no definition
- **Diagnosis**: Chunking miss — the SONAR definition and full explanation 
  got split across chunks. The top-1 has context around SONAR but not 
  the direct definition. This is a **chunk boundary failure**.

## Query 4: "What is the frequency of sound?" (partial)
- **Top-1 chunk_id**: chunk_0031
- **Score**: 0.9057
- **Issue**: Chunk contains garbled text with symbols (λλλλ) — 
  PDF extraction artifact from a figure page.
- **Diagnosis**: This is a **mixed structure failure** — figure labels 
  and symbols got mixed into the text chunk during extraction.
