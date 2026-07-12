# Glossary

This glossary defines the terminology used throughout the VerySynh architecture. Unless stated otherwise, these definitions apply consistently across all project documentation.

---

# Architecture

## VerySynh

A neuro-symbolic architecture that separates hypothesis generation, structured memory, and structural verification into independent system responsibilities.

Instead of storing answers, VerySynh stores **verifiable solution patterns**. A language model generates hypotheses, while external memory and deterministic verification determine whether the reasoning corresponds to accumulated validated experience.

---

## Large Language Model (LLM)

The generative component responsible for producing hypotheses, synthesizing responses, and combining known solution patterns.

The LLM does **not** perform structural verification and is not considered the authoritative source of accumulated knowledge.

---

## Memory

An external structured knowledge layer that stores solution patterns, negative cases, relationships, confidence values, and contextual metadata.

Unlike a traditional RAG database, the memory stores **how problems are solved**, not textual answers.

---

## Orchestrator

The deterministic control component responsible for coordinating the interaction between the LLM, the Unifier, and Memory.

The Orchestrator determines whether a generated reasoning trajectory is compatible with verified structural patterns and decides whether the response should be accepted, rejected, or treated as a new hypothesis.

---

## Unifier

A deterministic normalization module that extracts the structural representation of a solution.

Depending on the domain, the Unifier may analyze source code, logical structures, or domain-specific representations.

Its responsibilities include:

- extracting the structural skeleton;
- replacing identifiers with semantic role tokens;
- identifying structural bases;
- generating a pattern signature;
- computing a Structure Digest.

The Unifier operates independently of any LLM.

---

## Reflection Agent

A background process responsible for maintaining the quality of accumulated knowledge.

Its responsibilities include:

- merging equivalent patterns;
- discovering structural relationships;
- identifying emerging Gold Standards;
- detecting obsolete or conflicting patterns;
- updating confidence values.

The Reflection Agent performs structural analysis rather than language generation.

---

# Structural Model

## Basis

The smallest universal structural operation recognized by the architecture.

Examples include:

- filtering;
- transformation;
- aggregation;
- branching;
- iteration;
- search;
- isolation;
- synchronization.

Bases are domain-independent and serve as the structural alphabet from which more complex solution patterns are composed.

The initial implementation assumes a small finite vocabulary (approximately 16–20 bases), which may evolve as the architecture matures.

---

## Pattern

A normalized representation of a single solution or failure case.

Each pattern contains:

- Pattern Signature;
- Structure Digest;
- normalized structural representation;
- context tags;
- confidence value;
- relationships to other patterns.

Patterns represent reusable reasoning structures rather than textual responses.

---

## Pattern Signature

A compact structural identifier describing the set of Bases involved in a pattern.

Typically represented as a bit mask.

Pattern Signatures provide fast candidate retrieval but are not sufficient for determining structural equivalence.

---

## Structure Digest

A hash computed from the normalized structural representation of a pattern.

The Structure Digest enables:

- duplicate detection;
- exact structural identity comparison;
- efficient indexing.

Unlike Pattern Signatures, Structure Digests uniquely identify normalized structures.

---

## Structural Invariance

The property that structurally equivalent reasoning remains equivalent after normalization regardless of syntax, naming, or representation.

For example, two implementations written in different programming languages may be structurally invariant if they express the same reasoning pattern.

---

## Trajectory

A structured sequence or graph of reasoning stages leading from an initial state to a final result.

VerySynh validates trajectories rather than textual outputs.

Multiple trajectories may solve the same task.

---

## Partial Order

The dependency structure between mandatory stages of a trajectory.

VerySynh considers two trajectories equivalent when they preserve the same critical partial order, even if intermediate steps differ.

---

## Context Tags

Structured metadata describing the conditions under which a pattern or relationship is applicable.

Examples include:

- input size;
- hardware constraints;
- programming language;
- medical protocol;
- legal jurisdiction.

Context Tags allow structurally identical patterns to behave differently under different conditions.

---

# Knowledge

## Gold Standard

A highly validated solution pattern representing one accepted trajectory for a given problem class.

A task may have multiple Gold Standards when several alternative trajectories are independently validated and considered equally acceptable.

---

## Negative Case

A documented failure pattern.

Negative Cases are stored alongside successful patterns and allow the system to recognize previously observed mistakes, unsafe reasoning, or undesirable outcomes.

---

## Confidence

A numerical estimate describing the maturity of accumulated evidence.

Confidence is maintained independently for:

- patterns;
- relationships between patterns.

Confidence reflects accumulated validation rather than objective truth.

---

# Relationships

Patterns may be connected through typed relationships.

Common relationship types include:

- equivalent
- safer_than
- faster_than
- specializes
- generalizes
- requires
- conflicts_with
- supersedes

Relationships may also contain Context Tags describing the circumstances under which they hold.

---

# Methods

## Deterministic Method

A method whose output is completely determined by its input and contains no stochastic behavior.

Examples include:

- structural normalization;
- Pattern Signature generation;
- Structure Digest computation;
- tree edit distance;
- graph comparison.

---

## Heuristic Method

A probabilistic or approximate method used when exact deterministic comparison is impractical.

Examples include:

- embedding-based retrieval;
- semantic similarity search;
- hypothesis generation performed by an LLM.

Heuristic methods may suggest candidate solutions but do not determine structural correctness.

---

# Validation

## Structural Validation

The process of determining whether a generated reasoning trajectory belongs to one of the known admissible structural classes.

Structural Validation evaluates normalized representations rather than textual similarity.

---

## Structural Equivalence

Two patterns are structurally equivalent if they preserve the same essential reasoning structure after normalization.

Structural equivalence does not require identical syntax, implementation, or wording.

---

# Maturity Levels

## Hypothesis

A newly observed pattern with limited validation.

Typical confidence:

≈ 0.3

---

## Working Pattern

A pattern confirmed by multiple successful observations.

Typical confidence:

≈ 0.7

---

## Verified Pattern

A repeatedly confirmed pattern with high confidence.

Typical confidence:

≈ 0.9

---

## Gold Standard

The highest maturity level representing broadly validated structural knowledge.

Typically:

> 0.95 confidence

although multiple Gold Standards may coexist for the same problem class.
