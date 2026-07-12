
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

- `docs/architecture.md` — Full architecture specification
- `docs/theory.md` — Formal model, invariants, guarantees
- `docs/faq.md` — Frequently asked questions
- `prototype/` — Future prototype (currently placeholder)

---

## License

All rights reserved. © Horiless (GitHub: Whiseb, Telegram: @mindress). Use, copying, distribution, or modification of this architecture is permitted only with the written permission of the author.
