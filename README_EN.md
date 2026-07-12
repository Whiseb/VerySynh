1. What is VerySynh
VerySynh is an architecture that divides the problem-solving process between two agents:

Model (Intuitive Agent) — generates hypotheses, synthesizes answers, searches for new pattern combinations.

Memory (Skeptical Agent) — verifies hypotheses, presents counterexamples and negative cases, offers alternatives.

They work in a dialectical cycle: model proposes → memory verifies → model corrects → cycle repeats until a consensus solution is reached. If no agreement is reached, the system honestly reports: "Unable to confirm the solution."

This is not RAG with verification. This is a symbiosis where memory is not a passive archive, but an active participant in reasoning with veto power.

2. Why This is Needed
Modern LLMs hallucinate because they do not verify facts. RAG inserts relevant documents into the prompt, but the model can ignore them. Agents (AutoGPT and similar) let the model command tools but do not verify its conclusions.

VerySynh solves this architecturally: knowledge is extracted from model weights into external structured memory, and the generation and verification processes are separated between two specialized components.

3. System Components
3.1 Unifier
Task: Extract a structural skeleton from input material, cleaned of specific names and data.

How it works:

Parses input material via tree-sitter (for code) or another domain-specific parser.

Extracts AST and replaces all identifiers with role tokens: user_input, temp_var, result_list, counter, flag.

Determines which bases are involved: loop, filter, transform, aggregate, branch, isolate, queue, recursion, composition, and others.

Forms a signature — a bitmask of bases (16 bits).

Returns a skeleton without original names, strings, or data.

Why without LLM: tree-sitter is a fast C parser, works in milliseconds, consumes no tokens, does not hallucinate.

Interchangeability: The Unifier is a replaceable component. Each domain (code, medicine, engineering, psychology) has its own parser. Bases and memory structure remain the same.

For unstructured text: LLM can act as a normalization tool under human supervision — not as a source of truth, but as an assistant in extracting structure. The normalization result is verified by a human expert before being saved to memory.

3.2 Memory
Task: Store not raw text, but structural patterns with confirmation history, negative cases, and links.

Record structure:

id: unique identifier (e.g., p487)

basis_mask: bitmask of bases

structure_digest: 64-bit hash of normalized AST

l1_summary: brief description (up to 120 characters)

l2_detail: compressed pseudocode (only for golden standards)

l3_full: full change history (only for cold archive)

confidence: number from 0.0 to 1.0

maturity: verified | working | hypothesis | rejected | negative_case

links: connections to other patterns (alternatives, negative cases, parent standard, sources)

context_tags: usage context (language, version, environment)

Indexes:

Bitmask — instant filtering of 90%+ candidates (O(1)).

Structure hash — exact duplicate search (O(1)).

Vector index (ChromaDB) — semantic search for similar patterns.

Access levels:

L1 (always, 50–80 tokens per pattern): id, l1_summary, basis_mask, maturity, confidence. For quick responses.

L2 (on model request or in "Deep Analysis" mode): l2_detail. For complex synthesis.

L3 (only for background analysis): full link graph, change history, raw examples.

Protection against becoming a dump:

On-the-fly deduplication (structure hash).

Aggregation into golden standards (Reflection Agent).

Confidence grows only with multiple confirmations.

Negative cases are explicitly separated from working patterns.

Maturity levels prevent confusing a hypothesis with a verified solution.

3.3 Model
Task: Generate hypotheses, synthesize answers based on memory facts, search for new pattern combinations.

Interchangeability: The Model is a replaceable component. Virtually any language model works: local (on your own hardware), cloud (via API), or future models — without changing the architecture. VerySynh is not tied to a specific provider or version. The model can be swapped at any time; memory and orchestrator remain the same.

Why is a model needed if memory is primary? The model is needed for three things that memory cannot do: intuition (seeing unformalized connections), synthesis (assembling an answer from several patterns), hypotheses (proposing solutions for new situations where memory is empty). The model is not a competitor to memory, but its partner. Memory provides precision, the model provides flexibility.

3.4 Reflection Agent
Task: Background aggregation of patterns into golden standards.

When it runs: when the computer is idle (at night, in the background).

What it does:

Groups patterns with identical signatures and similar structures (tree edit distance).

When enough similar patterns accumulate, creates a golden standard (maturity = verified).

Original patterns are not deleted, but marked as aggregated_into: gold_id.

Analyzes user profile: which bases are used, which are avoided.

Generates unobtrusive insights: "You avoid recursion. Here is a task where it would simplify the code."

Protection against erroneous golden standards:

Aggregation occurs only when a sufficient number of confirmed patterns accumulate.

Confidence of a golden standard is calculated as the average confidence of source patterns.

If a new negative case appears, the golden standard's confidence may drop, and it will be reviewed.

3.5 Orchestrator
Task: Implement the dialectical cycle and memory veto power.

Work cycle:

User submits a query.

Unifier extracts the signature from the query.

Memory searches for patterns with the same signature (bitmask → hash → embedding).

Orchestrator assembles the prompt: L1 patterns + negative cases + instruction "rely only on these facts."

Model generates a response.

Post-generation verification: Unifier extracts the skeleton from the model's response. If the response structure does not match the golden standard structure, the response receives LOW CONFIDENCE or NOT VERIFIED status.

If memory finds an insurmountable contradiction, the response is not delivered. The system reports: "Unable to confirm the solution."

If the response is confirmed, it is delivered to the user.

Decision rules:

If pattern confidence < 0.3 and the model insists — allow, but mark as LOW CONFIDENCE.

If confidence > 0.9 and the model contradicts — block the response.

If a negative case exists and the model ignores it — block the response.

If no agreement is reached after N iterations — issue an honest refusal.

4. How the System Learns (Evolution of Trust)
VerySynh does not train the model. It trains the memory.

Data → Verification → Structural memory becomes richer → Model uses higher-quality memory

The system's intelligence grows not by changing model weights, but by accumulating verified patterns and negative cases.

Three stages of evolution:

Stage 0 — Borrowed Intelligence. Memory is empty. The system works as a regular AI assistant, using the model's knowledge. Successful solution skeletons are saved in the background. Maturity: hypothesis, working.

Stage 1 — Symbiosis. Memory has accumulated enough patterns. Model and memory work in a dialectical cycle. Maturity: verified appears.

Stage 2 — Memory Dominance. In known areas, memory becomes the primary source of truth. The model becomes a synthesis interface between confirmed knowledge. For new tasks, it still generates hypotheses.

Protection against stagnation: Confidence is dynamic — it can drop when new negative cases appear. The hypothesis level allows storing unconfirmed ideas. Aging mechanism: if a pattern is unused for a long time, its confidence gradually decreases.

5. Two-Phase Launch (Cold Start Solution)
Phase 1: Passive Collection. The system works as a regular AI assistant. The user is unaware of the memory. Successful solution skeletons are saved in the background. No quality degradation at launch.

Phase 2: Active Verification. When enough golden standards have accumulated, the system begins mandatory memory verification before every response. The user notices that the model has stopped hallucinating.

How long until Phase 2: Depends on domain and number of users. For popular programming languages with active use — weeks. For narrow domains — months. But the system does not require waiting: it is useful from day one, simply becoming more useful over time.

6. What the System Tells the User
Instead of "I don't know," the system provides an honest answer about its degree of certainty:

Exact match (maturity: verified, confidence > 0.9): "This is a verified solution. Here is the golden standard, here is the confirmation history, here are the known risks."

Analogy found (maturity: working, confidence 0.5–0.9): "No exact match, but there is a similar case. Here is what worked there, here is what didn't. Trying to adapt."

Neither (maturity: hypothesis): "This is a new situation. Building a hypothesis based on known principles. Warning about possible risks."

Contradiction found: "Unable to confirm the solution. A negative case has been found that contradicts the proposed hypothesis."

7. What VerySynh Promises and Does Not Promise
Promises:

Significant reduction of hallucinations in areas covered by memory.

Honest confidence assessment for every response.

Continuous improvement without model retraining.

Operation on modest hardware (thin model + smart memory).

Does not promise:

Complete elimination of hallucinations (in new areas where memory is empty, the model may still err).

Instant effect (memory requires time to accumulate).

Replacement of all existing AI tools (VerySynh is an architecture for tasks where accuracy and verifiability matter).

8. Technical Stack (Prototype)
Model: Any local or cloud LLM, connected via standard API.

Unifier: tree-sitter (Python, JavaScript, HTML/CSS) or another domain parser.

Memory: SQLite (metadata) + ChromaDB (vector search).

Orchestrator: Python script (~300 lines).

Interface: Terminal for prototype; later — IDE plugin or web interface.

9. Roadmap
Month 1–2: Unifier + Memory

tree-sitter parsing, AST extraction, basis classification.

SQLite + ChromaDB, record structure, indexes.

Month 3–4: Model + Orchestrator

Prompt with L1/L2 patterns.

Dialectical cycle (query → search → generation → post-verification).

Terminal interface.

Month 5–6: Reflection Agent

Background pattern aggregation.

Golden standard creation.

User profiling.

Month 7–9: Testing and Iterations

Unit tests for hallucinations.

User testing (10–20 people).

Measurements: error frequency with and without memory.

Month 10–12: Publication

Article / white paper.

Open source code.

Demo video.

10. Essence in One Sentence
VerySynh is an architecture where memory ceases to be a passive repository and becomes an independent participant in reasoning, capable of verifying, challenging, and confirming the model's hypotheses.

Architecture by: Horiless (GitHub: Whiseb, Telegram: @mindress)
License: CC BY 4.0
