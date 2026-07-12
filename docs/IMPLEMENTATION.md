# VerySynh — Implementation Tasks

This document describes the tasks required to implement a VerySynh prototype
on a capable model (7B+ with function calling).
It does not provide ready-to-use code. Instead, it defines what each component
must do, why, and what pitfalls to avoid — based on our own testing on small
models (<7B).

Anyone attempting to build VerySynh on a stronger model should use this as a
checklist and a guide.

---

## Task 1: Memory Pre-Loading

**Goal:** Memory must not be empty at system start.

**What to do:**
- Prepare a set of initial gold standards covering basic operations
  (filter, transform, aggregate, branch, isolate).
- Each gold standard must contain: operation type, input type, condition,
  expected output, and a human-readable summary.
- Store them in a structured format (JSON, SQLite, or vector DB).

**Why:**
A model cannot simultaneously learn to use memory and populate it from scratch.
Without pre-loaded knowledge, the system has nothing to verify against.

**Pitfalls:**
- Do not use raw text as memory entries. Structure is mandatory.
- Avoid keyword-only indexing. Semantic search is required for real use.

---

## Task 2: Deterministic Request Classification

**Goal:** Classify every user request without calling the LLM.

**What to do:**
- Implement a classifier that maps a request to an operation type and a
  condition.
- Use keyword heuristics, regex, or a tiny fine-tuned model.
- Return a structured object: `{"operation": "filter", "condition": "even"}`.

**Why:**
Small models fail to return consistent JSON. Even larger models benefit from
deterministic classification: it is fast, free, and never hallucinates.

**Pitfalls:**
- Do not rely on the main LLM for classification. It will add latency and
  instability.
- Classification must be robust to synonyms and word order.

---

## Task 3: Memory Search

**Goal:** Find relevant gold standards for the classified request.

**What to do:**
- Search memory by operation type first (exact match on mask or category).
- Narrow down by condition and input type if available.
- Return the best match (or top-N matches) as structured context.

**Why:**
The model must receive only relevant knowledge. Dumping the entire memory
into the context wastes tokens and confuses the model.

**Pitfalls:**
- Do not use request text as the search key. Requests with identical meaning
  will have different wording.
- Semantic search (embeddings) is preferred over exact text matching.

---

## Task 4: Context Assembly

**Goal:** Build the prompt for the main LLM using memory results.

**What to do:**
- Include a system prompt that instructs the model: answer in the target
  language, use memory if available, admit ignorance if memory is empty.
- Include the matched gold standard(s) in a dedicated `[Memory]` block.
- Include the original user request.
- Keep total prompt length within the model's effective context window.

**Why:**
The model must see both the request and the verified knowledge at the same
time. If memory is missing, the model must be told it is allowed to say
"I don't know."

**Pitfalls:**
- Do not hide memory context in a place the model might ignore.
- Avoid overloading the prompt with irrelevant memory entries.

---

## Task 5: Response Verification

**Goal:** Check the model's answer against the gold standard.

**What to do:**
- After the model generates a response, compare it to the expected output from
  the gold standard.
- Verification should be **deterministic** (code, not LLM) for mathematical
  and logical operations.
- For open-ended responses, a second LLM call may be used — but only with a
  different, smaller model or strict instructions.

**Why:**
Small models hallucinate during verification. A model cannot reliably check
itself. Deterministic verification eliminates this risk.

**Pitfalls:**
- Do not use the same model instance for both generation and verification.
- If using LLM for verification, provide the gold standard as absolute truth
  and instruct the verifier to treat any deviation as an error.

---

## Task 6: Memory Update

**Goal:** Persist new, verified patterns for future use.

**What to do:**
- After successful verification, store the new pattern in memory.
- Assign a confidence score: low for first occurrence, high for repeated
  confirmations.
- Link the pattern to the gold standard it was verified against.

**Why:**
The system improves with use. Every verified interaction enriches the
collective memory.

**Pitfalls:**
- Do not overwrite existing gold standards. New patterns are separate entries.
- Avoid storing raw user input. Store the structured skeleton instead.

---

## Task 7: Handling Empty Memory

**Goal:** Define system behavior when no gold standard is found.

**What to do:**
- If memory search returns nothing, the model must respond with a variation of
  "I don't have verified knowledge on this. Here is my best guess, but please
  verify it yourself."
- The unverified response is saved as a hypothesis with low confidence.

**Why:**
Honesty is the core principle of VerySynh. The system must never pretend to
know something it does not.

**Pitfalls:**
- Do not let the model generate a confident-sounding but unverified answer
  without a disclaimer.
- Do not save hypotheses as gold standards.

---

## Task 8: Reflection and Aggregation

**Goal:** Periodically clean and improve memory.

**What to do:**
- Run a background process that groups similar patterns.
- When enough similar patterns accumulate, create a new gold standard.
- Mark outdated or contradicted patterns for review.

**Why:**
Memory grows indefinitely. Without aggregation, it becomes noisy and slow.

**Pitfalls:**
- Aggregation must be conservative. Do not merge patterns unless the match is
  structural, not just textual.
- Keep a history of changes for auditability.

---

## Task 9: Model Training for Memory Interaction

**Goal:** The model must be fine-tuned to work with external memory.

**What to do:**
- Create a synthetic dataset of interactions where the model:
  1. Receives a request.
  2. Explicitly queries memory (or notes its absence).
  3. Generates a response based on memory content.
  4. Verifies and saves the result.
- Fine-tune the model on this dataset.

**Why:**
Current models are not trained to interact with external memory. Without
fine-tuning, they treat memory context as optional suggestions, not as
required facts.

**Pitfalls:**
- Do not skip this task. Prompt engineering alone is insufficient.
- The dataset must include negative examples: "I don't know" responses,
  handling of empty memory, and correction of initial guesses.

---

## Task 10: Function Calling Integration

**Goal:** Enable the model to call memory functions programmatically.

**What to do:**
- Expose memory operations as callable functions:
  `search_memory(operation, condition)`, `save_pattern(pattern)`,
  `verify_answer(answer, gold_standard)`.
- Use the model's native function calling capability.
- The orchestrator executes the function and returns the result to the model.

**Why:**
Function calling ensures the model interacts with memory in a structured,
reliable way — not by generating free-form text that must be parsed.

**Pitfalls:**
- Do not let the model hallucinate function calls that do not exist.
- Validate all function inputs before execution.

---

## Summary

Building VerySynh is not a single task. It is a sequence of interconnected
components, each solving one part of the core problem: **making a model stop
lying by giving it a memory it must consult.**

Our prototype proved the architecture works. It also proved that small models
(<7B) are insufficient — not because the idea is flawed, but because they lack
the capacity to use memory effectively.

A capable model (7B+, function calling, long context) with the tasks above
implemented should produce a system that generates verified, trustworthy
responses — and honestly admits when it does not know.
