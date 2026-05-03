# Fix Memo — Stage 5

## Targeted Failure: q05
**Question:** What is the difference between loudness and pitch?
**Original answer (v1):** "The provided text does not contain any information..."
**Failure category:** Ambiguous query — corpus covers both concepts separately,
never as a direct comparison.

## Diagnosis
Retrieval returned chunk_0042 (pitch) and chunk_0045 (loudness) separately.
Neither chunk explicitly compares the two concepts together.
The chapter teaches them as separate topics, never as a direct comparison.

## Fixes Attempted
**Fix 1:** Increased k from 3 to 5
- Result: Chunks present but gemma:7b still refused
- Reason: Model could not synthesize comparison from separate chunks

**Fix 2:** Query decomposition
- Split "difference between loudness and pitch" into two sub-queries
- Retrieved better chunks but gemma:7b still refused

**Fix 3:** Switched model from gemma:7b to gemma2:2b
- Result: q05 RESOLVED 
- Answer: "Loudness is a perception of sound intensity, while pitch refers
  to the frequency of sound waves."
- Reason: gemma2:2b is better at synthesis from context despite smaller size

## Score Delta
| Metric | v1 | v2 | Delta |
|--------|----|----|-------|
| Correct | 8/9 | 9/9 | +1  |
| Grounded | 8/9 | 9/9 | +1  |
| OOS Refused | 3/3 | 3/3 | 0 |

## Key Insight
The fix that worked was not a retrieval fix — it was a model switch.
gemma2:2b handles synthesis queries better than gemma:7b despite being smaller.
This shows that model choice matters as much as retrieval strategy.

## What I would try in Wk11
Add explicit synthesis instruction to the prompt:
"If the question asks for a comparison, describe each concept separately
then compare them directly."
