# Prompt Comparison: Permissive vs Strict

## Permissive Prompt
"Answer the question using the context below."

### Results:
- Speed of sound: Answered correctly (no citation)
- Echo: Answered correctly (no citation)  
- Speed of light (OOS): Refused — but by Gemma's own judgment, not our instruction.
  This is LUCKY behavior, not guaranteed. A different model or query might hallucinate.

---

## Strict Prompt
"Answer ONLY using the context below.
After every factual claim, cite the chunk in square brackets.
If the answer is not present, reply exactly: 
'I don't have that in my study materials.'"

### Results:
- Speed of sound: Answered correctly WITH citation [chunk_0036] 
- Echo: Answered correctly WITH citation [chunk_0050] 
- Speed of light (OOS): Refused with explanation 

---

## Key Insight
The permissive prompt relies on the model's judgment for refusal.
The strict prompt makes refusal a HARD CONSTRAINT — not optional behavior.
Result: citations now present on every factual claim, 
and out-of-scope refusal is enforced by instruction, not luck.
