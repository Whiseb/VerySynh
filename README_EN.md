# VerySynh

**VerySynh stores not answers, but verifiable ways of arriving at them.**

VerySynh is an architecture where a language model generates hypotheses, and an external structured memory verifies them against accumulated experience. The memory stores structural patterns of solutions — classes of admissible trajectories — with all known alternatives, negative cases, and typed relationships between them.

The central theoretical principle is **structural invariance**: VerySynh verifies not textual similarity, but preservation of critical reasoning structure.

---

## Why not RAG or agents

- **RAG** retrieves text and adds it to the prompt. The model can ignore it, distort it, or use it incorrectly. Memory is passive and has no veto power.
- **Agents** make the model a commander that calls tools. If the model hallucinates during planning, errors multiply.

VerySynh separates generation from verification. The LLM proposes. The memory constrains. The orchestrator enforces.

Unlike RAG, VerySynh's memory is not advisory. It participates in deciding whether a generated solution is admissible.

---

## Core properties

1. **Structural normalization before comparison.** A Unifier extracts a structural skeleton — cleaned of names, syntax, and style.
2. **Patterns instead of text.** Memory stores structural solution skeletons, not raw text fragments.
3. **Deterministic structural validation with heuristic retrieval.** Comparison is algorithmic. Retrieval may be probabilistic, but the compliance decision is rule-based.
4. **Verification layer independent of the model.** The orchestrator and memory work with any LLM.

---

## How it works (simplified)
Query → Unifier → Signature → Memory Retrieval → LLM → Unifier → Verify → Accept / Reject / Hypothesize

1. **Unifier** extracts the structural skeleton and computes a normalized signature.
2. **Memory** returns all known patterns with the same signature — gold standards, alternatives, and negative cases.
3. **Orchestrator** compares the generated solution's structure against memory. It enforces the rules: match a gold standard → accept; match a negative case → reject; no match → save as hypothesis.
4. **Reflection Agent** (background, no LLM) aggregates similar patterns into gold standards and discovers new relationships.

---

## Two-axis model

**Abstraction levels:** Primitive → Composite → Procedure → Process
**Maturity levels:** Hypothesis → Working → Verified → Gold Standard

A pattern can be a Procedure at Working level or a Primitive at Gold Standard level. The axes are independent.

---

## The central principle

> **The invariant is not the sequence of steps, but the partial order of critical stages.**

Two solution trajectories are equivalent if they pass through the same mandatory stages and achieve the same result — even if intermediate steps differ.

---

## Guarantees

**What VerySynh guarantees:**
- ✔ Compares structures, not texts.
- ✔ Does not depend on a specific LLM.
- ✔ Stores negative cases alongside successful ones.
- ✔ Allows multiple gold standards for the same task.
- ✔ Honestly reports when no verified pattern exists.

**What it does NOT guarantee:**
- ✘ Absolute truth — only compliance with accumulated experience.
- ✘ Absence of errors — the system errs if the accumulated experience errs.
- ✘ Operation without a Unifier — structural normalization is mandatory.
- ✘ Solving unique creative tasks — no verified patterns exist there.

---

## Applicability boundaries

VerySynh is most effective where:
- Structural repeatability of solutions exists.
- Formal structural normalization is possible.
- Errors are documented and can be saved as negative cases.

Domains: programming, engineering, medical protocols, legal norms, standard business processes.

---

## Project status

VerySynh is an **architectural specification**, not a production implementation.

- Architectural design
- Formalization of concepts
- Discussion and review
- Prototype planning

**My role:** I am the author of the architecture and conceptual model. The implementation is a separate engineering task. This repository documents the system design and serves as a reference for future collaboration.

The purpose of this repository is to **validate the architecture first, then implement a prototype**.

---

## Structure

- `architecture.md` — Full architecture specification
- `theory.md` — Formal model, invariants, guarantees
- `faq.md` — Frequently asked questions

---

## License

Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International
(CC BY-NC-ND 4.0)

© Horiless (GitHub: Whiseb, Telegram: @mindress).

You are free to:
- Share — copy and redistribute the material in any medium or format.

Under the following terms:
- Attribution — You must give appropriate credit.
- NonCommercial — You may not use the material for commercial purposes.
- NoDerivatives — If you remix, transform, or build upon the material,
  you may not distribute the modified material.

Full license: https://creativecommons.org/licenses/by-nc-nd/4.0/legalcode
