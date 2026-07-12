# VerySynh — Architecture Specification

**VerySynh stores not answers, but verifiable ways of arriving at them.**

---

## 1. Core components

| Component | Function |
|-----------|----------|
| **LLM** | Generates hypotheses, synthesizes answers, combines patterns. |
| **Memory** | Stores structural patterns of solutions, negative cases, and typed relationships between them. |
| **Orchestrator** | Compares the structure of the hypothesis with memory. Decides whether the answer is admissible. |

---

## 2. Unifier — extracting structure

Before saving or comparing anything, the system normalizes the solution:

- Extracts AST or logical graph.
- Replaces variable names with role tokens: `counter`, `buffer`, `flag`, `user_input`.
- Identifies the bases involved: filtering, transformation, aggregation, branching, isolation, queuing, recursion.
- Forms a signature — a bitmask of bases.
- Computes a structure digest — a hash of the normalized structure.

All of this is performed **without using an LLM**, using static analyzers (tree-sitter for code, specialized parsers for other domains).

---

## 3. Memory — a graph of trajectories, not a catalog of answers

**Task Z may have several admissible trajectories:**

Task Z
├── Pattern A — safer, longer
├── Pattern B — faster, risk X
└── Pattern C — experimental


**Relationships between patterns are typed:**

| Relationship | Meaning |
|--------------|---------|
| `equivalent` | Same result |
| `safer_than` | Safer |
| `faster_than` | Faster |
| `generalizes` | More general |
| `specializes` | Special case |
| `requires` | Requires a prior pattern |
| `conflicts_with` | Incompatible |
| `supersedes` | Replaces an old one |

Each relationship can be context-dependent. Context tags are a mandatory part of every relationship.

**Confidence is applied at two levels:**
- `confidence(pattern)` — how well the pattern itself is confirmed.
- `confidence(edge)` — how well the relationship is confirmed.

---

## 4. Orchestrator — verifying compliance, not text

**Verification cycle:**

1. The LLM generates an answer.
2. The Unifier extracts the structure and signature from the answer.
3. The memory returns all patterns with the same signature, including negative cases and relationships.
4. The orchestrator compares the structure of the answer with the reference structures.
5. If the structure matches a gold standard **and context tags are compatible** — the answer is accepted.
6. If the structure matches a negative case — the answer is rejected, the model receives a warning.
7. If the structure matches nothing — the answer is accepted as a hypothesis (confidence 0.3) and saved for verification.

Memory does not decide. It provides constraints that the orchestrator enforces.

---

## 5. Reflection Agent — memory evolution

A background process that does not use an LLM:

- Groups patterns with the same signature and similar structure.
- When enough confirmations accumulate, creates a gold standard.
- Discovers new relationships between patterns and creates typed links.
- Identifies outdated standards and marks them for revision.

---

## 6. Two-axis model

**Abstraction levels:**

| Level | Name | Example |
|-------|------|---------|
| 1 | **Primitive** | Filtering, Aggregation, Branching |
| 2 | **Composite** | Filter → Transform → Aggregate |
| 3 | **Procedure** | Differential diagnosis |
| 4 | **Process** | Complete treatment protocol |

**Maturity levels:**

| Level | Name | Description |
|-------|------|-------------|
| 1 | **Hypothesis** | Single solution, confidence 0.3 |
| 2 | **Working** | Confirmed by several users, confidence 0.7 |
| 3 | **Verified** | Confirmed many times, confidence 0.9 |
| 4 | **Gold Standard** | Aggregated from many confirmed, confidence > 0.95 |

---

## 7. Structural invariance

> **The invariant is not the sequence of steps, but the partial order of critical stages.**

Two trajectories are equivalent if they pass through the same mandatory stages and achieve the same result.

---

## 8. Guarantees

**What VerySynh guarantees:**
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

## 9. Applicability boundaries

VerySynh is most effective where structural repeatability exists, formal normalization is possible, and errors are documented.

Domains: programming, engineering, medical protocols, legal norms, business processes.

---

## 10. Technology stack (planned)

- **Model:** Ollama + Qwen 2.5 (4B, Q4_K_M)
- **Unifier:** tree-sitter (Python)
- **Memory:** SQLite + ChromaDB
- **Orchestrator:** Python microservice
- **Reflection Agent:** background CPU process
