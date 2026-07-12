# VerySynh Prototype Analysis on Small Models (<7B)

## Objective

To determine whether small local LLMs (1.5B–4B parameters) can operate within
the VerySynh architecture: classifying requests, utilizing external memory,
verifying responses, and avoiding hallucinations.

## Configuration

- **Models:** Qwen 2 1.5B, Qwen 2 4B, Gemma 2 2B
- **Memory:** Local JSON file (`data/memory.json`)
- **Orchestrator:** Python, keyword-based classification, memory search,
  LLM-based verification
- **Constraint:** Small models were deliberately chosen to test the lower bound
  of the architecture's operability.

---

## Issues Identified

### 1. Inability to Interact with Memory

Small models have not been trained to query external sources. They treat every
prompt as an isolated request and fail to retain context across calls.

**Example:** The model receives `[Memory]: filter a list...` but ignores the
hint and generates a new response without incorporating prior experience.

**Conclusion:** Models must be explicitly fine-tuned for memory interaction:
when to search, how to apply retrieved knowledge, and how to persist new
patterns.

---

### 2. Catastrophic Context Loss

Models with short effective context windows forget the beginning of a prompt
before generation completes. During verification, the model "forgets" the
reference standard and begins comparing the answer against itself.

**Example:** In `_verify` logs, the model returns the reference instead of a
corrected answer due to role confusion.

**Conclusion:** VerySynh requires a context window of at least 8K tokens and
a model capable of maintaining complex, multi-part prompts.

---

### 3. Hallucination During Verification

The model tasked with verifying an answer hallucinates itself. It may "correct"
a right answer into a wrong one or confirm an erroneous response.

**Example:** Request: "filter array [10, 20, 30] and keep only odd numbers."
Correct result is an empty array. The model outputs `[20, 30]`, and the
verifier confirms it.

**Conclusion:** A weak model cannot serve as both generator and verifier.
Either separate models or a deterministic (code-based) verifier is required.

---

### 4. Handling of Empty User Input

When receiving empty input, the model does not discard it but attempts to
respond as if a valid request was made, producing greetings or generic text.

**Example:** After an empty line, the model replies: "Hello! Please ask your
question."

**Conclusion:** The model cannot distinguish between a missing request and a
greeting. This behavior must be filtered at the orchestrator level or
addressed during training.

---

### 5. Unstable JSON Output for Structure Extraction

The model is unable to consistently return valid JSON. It wraps JSON in
markdown formatting or appends extraneous text.

**Example:** Model output: ````json\n{"operation": "filter", ...}\n````

**Workaround:** A regular expression was added to extract JSON from responses.
This functions but confirms that small models fail to adhere to strict output
formats.

**Conclusion:** Production use requires native function calling or a
deterministic classifier.

---

### 6. Mask Collisions and Memory Overwrites

When using `basis_mask` as the key for pattern storage, all requests sharing
the same mask (e.g., `FILTER = 2`) overwrote each other.

**Workaround:** The key was replaced with a hash of the request text. This is
a temporary measure; production requires semantic search rather than exact
text matching.

**Conclusion:** Memory indexing is a distinct engineering challenge and cannot
be solved with ad-hoc methods.

---

### 7. Model Self-Looping

Without external memory, the model either hallucinates or enters endless
reflection loops, rephrasing the same output repeatedly. It requires an
external circuit that says: "Stop. Check the facts."

**Conclusion:** External memory is not an enhancement but a necessity. It is
only effective, however, when the model is capable of utilizing it.

---

## Conclusions

1. **Small models are unsuitable for VerySynh in their current form.** They
   cannot interact with memory, lose context, hallucinate during verification,
   and produce unstable structured output.
2. **The architecture is sound.** All components (classification, memory,
   search, verification) were implemented and connected in a working loop. The
   system operated as intended — at the very edge of the model's capabilities.
3. **A specialized model is required.** A model trained to work with memory:
   search, read, persist, and verify. Without this, VerySynh remains a concept.
4. **Memory must be pre-loaded.** The model cannot start with empty memory and
   simultaneously learn to populate it. Initial gold standards are necessary.
5. **A model should not verify itself.** Verification must be either
   deterministic (code) or performed by a separate model instance.

---

## Recommendations

- For prototyping, use models 7B+ with native function calling.
- Fine-tune a model specifically for external memory interaction.
- Pre-populate memory with foundational gold standards.
- Decouple generation from verification.
- Implement semantic memory search.

---

## Note on Model Scale

While 7B+ models are a significant step up from those tested here, they may
still prove insufficient. The cognitive demands placed on a model within
VerySynh — simultaneous context retention, memory search and retrieval,
synthesis of verified patterns, and honest admission of ignorance — may require
a substantially more capable model than the 7B class can offer. The lower bound
for a fully functional system is likely higher, and the architecture should be
designed to accommodate models as they continue to advance in both reasoning
ability and context fidelity.

---

## Status

The VerySynh prototype **validated the architectural approach** and **identified
the minimum requirements** for model and memory. The next phase is
implementation on a 7B+ model with function calling and pre-loaded memory.
Further research is required to determine the exact model capability threshold
for full system functionality.
