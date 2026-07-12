# Frequently Asked Questions (FAQ)

## General

### What is VerySynh?

VerySynh is an architectural proposal that separates hypothesis generation, structured memory, and structural verification into independent system responsibilities.

Instead of storing answers, it stores **verifiable solution patterns** and validates generated reasoning against accumulated structural experience.

---

### Is VerySynh another LLM?

No.

VerySynh is not a language model.

It is an architecture that can work with different LLMs by adding an independent layer for structural memory and verification.

---

### How is VerySynh different from RAG?

Traditional RAG retrieves relevant documents and injects them into the prompt.

The language model remains responsible for deciding whether to use that information.

VerySynh works differently.

The memory stores structural solution patterns rather than text, and the orchestrator validates generated reasoning against those patterns.

The memory is part of the verification process rather than passive context.

---

### How is VerySynh different from agent frameworks?

Agent frameworks primarily coordinate tools through the language model.

VerySynh instead separates reasoning from verification.

The LLM proposes hypotheses, while verification is performed by independent deterministic components.

---

## Architecture

### Does VerySynh require a large language model?

No.

The architecture is intentionally model-agnostic.

A stronger model may generate better hypotheses, but structural verification does not depend on model size.

---

### Can VerySynh work with any LLM?

Yes.

The verification layer is independent of the underlying language model and can be integrated with GPT, Llama, Qwen, or other compatible models.

---

### Can VerySynh work without a Unifier?

No.

Structural normalization is a mandatory part of the architecture.

Without a Unifier, structurally equivalent solutions cannot be recognized as belonging to the same pattern.

---

### What does the Reflection Agent do?

The Reflection Agent continuously analyzes accumulated patterns.

It can:

- merge equivalent patterns;
- identify emerging gold standards;
- discover relationships between patterns;
- detect obsolete or conflicting patterns.

---

## Memory

### Does VerySynh store knowledge?

Not directly.

It stores **verified ways of solving problems**, represented as structural patterns rather than textual facts.

---

### Does the memory store failed solutions?

Yes.

Negative cases are treated as first-class knowledge.

Remembering failures helps prevent repeating previously observed mistakes.

---

### Can one task have multiple correct solution patterns?

Yes.

VerySynh allows multiple verified trajectories for the same objective.

Different patterns may represent trade-offs such as safety, performance, simplicity, or domain-specific constraints.

---

### How does the system improve over time?

The language model itself is not retrained.

Instead, the structured memory grows, relationships become richer, and validated patterns gradually mature into higher-confidence knowledge.

---

## Verification

### What happens if no verified pattern exists?

The system reports that no verified solution is available.

It may still generate the best available hypothesis while explicitly communicating uncertainty.

---

### Does VerySynh guarantee correctness?

No.

VerySynh verifies **consistency with accumulated verified experience**, not absolute truth.

If the stored experience is incomplete or incorrect, verification may also be incomplete.

---

### Does structural verification use another LLM?

No.

Pattern comparison is intended to be deterministic.

Retrieval of candidate patterns may use heuristic techniques (such as embeddings), but structural validation itself is algorithmic.

---

## Domains

### What domains can VerySynh support?

The architecture is domain-agnostic.

Programming is the initial target because structural normalization is well understood.

Other domains—such as medicine, law, engineering, or business processes—require specialized Unifiers.

---

### Can VerySynh solve creative problems?

Not reliably.

For tasks without reusable structural patterns, VerySynh behaves similarly to a conventional LLM and explicitly reports lower confidence.

---

## Project

### Is VerySynh implemented?

Not yet.

The project is currently an architectural specification and research proposal.

Prototype implementation and experimental validation are planned future stages.

---

### Can I contribute?

Yes.

Architectural discussion, documentation improvements, prototype implementations, experiments, and constructive criticism are welcome.

See **CONTRIBUTING.md** for contribution guidelines.
