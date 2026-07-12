# Theory of VerySynh

## Structural invariance

> **The invariant is not the sequence of steps, but the partial order of critical stages.**

---

## Formal model

### Definitions

- **Q** — user query.
- **U(x)** — normalization function (Unifier).
- **Σ(x)** — basis signature (bitmask).
- **D(x)** — structure digest (hash of normalized structure).
- **M** — memory: a set of all known patterns.
- **M(Σ)** — subset of patterns with a given signature.
- **C** — candidates: C = Retrieve(M, Σ, embedding).
- **R** — model response: R = LLM(Q, C).
- **O** — orchestrator decision function: O(R, C) → {accept, reject, hypothesize}.

### Verification cycle

Q → U(Q) → Σ(Q) → Retrieve(M, Σ(Q)) → C
↓
LLM(Q, C) → R
↓
U(R) → Σ(R), D(R)
↓
O(R, C) → Accept / Reject / Hypothesize


### Decision rules

- **Accept:** D(R) matches a Gold Standard AND context tags are compatible.
- **Reject:** D(R) matches a Negative Case.
- **Hypothesize:** D(R) matches nothing — save as Hypothesis (confidence 0.3).

---

## Structural equivalence

Patterns P₁ and P₂ are structurally equivalent (P₁ ≈ P₂) if:

1. They achieve the same goal.
2. They satisfy the same mandatory stages (partial order).
3. They differ only in permissible variations.

---

## Guarantees

**What the system guarantees:**
- ✔ Compares structures, not texts.
- ✔ Does not depend on a specific LLM.
- ✔ Stores negative cases alongside successful ones.
- ✔ Allows multiple gold standards for the same task.
- ✔ Honestly reports when no verified pattern exists.

**What it does NOT guarantee:**
- ✘ Absolute truth.
- ✘ Absence of errors.
- ✘ Operation without a Unifier.
- ✘ Solving unique creative tasks.

---

## Applicability boundaries

Effective where structural repeatability exists, formal normalization is possible, and errors are documented.

---

## Future research

- Automatic discovery of new bases.
- Cross-domain pattern transfer.
- Federated learning for symbolic structures.
- Formal proofs of structural equivalence properties.
