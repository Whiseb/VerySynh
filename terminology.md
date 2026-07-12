# Terminology

## VerySynh

A neuro-symbolic architecture that separates hypothesis generation, structured memory, and structural verification into independent system components.

---

## Large Language Model (LLM)

The generative component responsible for producing hypotheses and synthesizing responses.

---

## Memory

An external structured repository that stores solution patterns, negative cases, relationships, confidence values, and contextual metadata.

---

## Orchestrator

The deterministic component that coordinates interaction between the LLM, Memory, and the Unifier, and determines whether a generated solution is admissible.

---

## Unifier

A deterministic module that extracts a normalized structural representation from input data and generates a Pattern Signature and Structure Digest.

---

## Reflection Agent

A background process responsible for aggregating patterns, discovering relationships, promoting mature patterns, and maintaining knowledge consistency.

---

## Basis

A universal structural operation used as the elementary building block of reasoning.

Examples include filtering, transformation, aggregation, branching, iteration, search, synchronization, and isolation.

---

## Pattern

A normalized representation of a single solution or failure case.

A Pattern contains structural information, metadata, confidence, and relationships.

---

## Pattern Signature

A compact representation of the Bases involved in a Pattern, typically encoded as a bit mask.

Used for fast candidate retrieval.

---

## Structure Digest

A hash computed from the normalized structure of a Pattern.

Used for exact structural identity and duplicate detection.

---

## Structural Skeleton

The normalized structural representation extracted by the Unifier.

It contains the essential reasoning structure while excluding implementation-specific details.

---

## Structural Invariance

The property that structurally equivalent solutions remain equivalent after normalization regardless of syntax, naming, or implementation.

---

## Structural Equivalence

Two Patterns are structurally equivalent if they preserve the same reasoning structure after normalization.

---

## Structural Validation

The process of verifying whether a generated reasoning trajectory belongs to a known admissible structural class.

---

## Trajectory

A structured sequence or graph of reasoning stages leading from an initial state to a solution.

---

## Partial Order

The dependency relationships between mandatory stages of a reasoning trajectory.

Equivalent trajectories preserve the same partial order while allowing variations in intermediate steps.

---

## Context Tags

Structured metadata describing the conditions under which a Pattern or relationship is applicable.

---

## Gold Standard

A highly validated Pattern representing one accepted solution trajectory.

Multiple Gold Standards may exist for the same problem class.

---

## Negative Case

A documented failure Pattern stored alongside successful Patterns to prevent repeated mistakes.

---

## Confidence

A numerical estimate representing the maturity of accumulated evidence for a Pattern or relationship.

---

## Relationship

A typed connection between two Patterns.

Examples include:

- equivalent
- safer_than
- faster_than
- generalizes
- specializes
- requires
- conflicts_with
- supersedes

---

## Deterministic Method

A method whose output is uniquely determined by its input and contains no stochastic behavior.

---

## Heuristic Method

A probabilistic or approximate method used for candidate retrieval or hypothesis generation when deterministic comparison is impractical.

---

## Primitive Pattern

A Pattern representing a single structural Basis.

---

## Composite Pattern

A Pattern composed of multiple Bases.

---

## Procedure

A domain-specific Pattern composed of multiple Composite Patterns.

---

## Workflow

A complete sequence of Procedures representing an end-to-end solution process.

---

## Hypothesis

A newly observed Pattern with limited validation.

---

## Working Pattern

A Pattern confirmed by multiple successful observations.

---

## Verified Pattern

A Pattern that has been repeatedly validated with high confidence.

---

## Pattern Maturity

The validation level of a Pattern, ranging from Hypothesis to Gold Standard.

---

## Pattern Abstraction

The structural complexity level of a Pattern, ranging from Primitive to Workflow.
