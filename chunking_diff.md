# Chunking Diff — Wk9 vs Wk10

## Wk9 Approach
- Used character-based splitting
- No content-type awareness
- No metadata on chunks
- Tables and worked examples split mid-sentence

## Wk10 Approach
- Token-aware sizing using tiktoken (max 250 tokens)
- Content-type-aware: prose / worked_example / question_or_exercise
- Rich metadata per chunk: {chunk_id, source, page, content_type, token_count}
- Paragraph-boundary-respecting splits

## Parameters
- Tokenizer: cl100k_base (matches text-embedding-3-small vocabulary)
- Max tokens: 250
- Split strategy: paragraph first, sentence fallback

## Results
- Total chunks: 69 from 24 pages
- Avg tokens: 191 (healthy, not too small)
- Content breakdown: prose 50, question_or_exercise 13, worked_example 6

## Where Content-Type Filtering Helped
- BM25 retrieval on "What is an echo?" — filtering to prose chunks
  avoided returning exercise chunks that mentioned echo tangentially
- worked_example chunks kept numerical calculations intact
  instead of splitting mid-equation

## Known Limitation
- Classifier uses keyword matching — "for example" in prose
  was initially misclassified as worked_example
- Fixed in v2 classifier with stricter regex patterns
  (requires "Example 10.X" format, not just the word "example")
