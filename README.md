# VerySynh Architecture

VerySynh is an architecture that divides the problem-solving process between two agents: a **Model** (Intuitive) and a **Memory** (Skeptical).

## Core Components

### Unifier
Parses input to extract structural skeletons, removing noise while identifying fundamental logic bases.

### Memory
Stores structural patterns, negative cases, and confirmation histories. Acts as an active participant with veto power.

### Model
Generates hypotheses and synthesizes answers based on the structural facts provided by memory.

### Reflection Agent
Aggregates patterns into "Golden Standards" during idle time.

### Orchestrator
Implements the dialectical cycle, assembling prompts, and performing post-generation verification.

## Evolution of Trust
The system learns by building richer structural memory, not by retraining the underlying model.

## Documentation

- 🇬🇧 Full Documentation → README_EN.md
- 🇷🇺 Полная документация → README_RU.md
- 📐 Architecture Specification → docs/architecture.md
- 📚 Theory → docs/theory.md
- ❓ FAQ → docs/faq.md
