# ADR 0003: Agent Reliability and Orchestration Efficiency Standard

## Status
Accepted

## Context
Review of session S20260505-001 revealed two critical failure patterns:
1. **Output Fidelity Gap:** Subagents (`explore` and `general`) interpreted tool results instead of reporting them. The `explore` agent produced a false negative regarding the `docs/` directory, and the `general` agent summarized stack traces, eroding trust and delaying the documentation phase.
2. **Orchestration Efficiency Gap:** The `architect-developer` followed a reactive "trial-and-error" pattern for resolving Dependency Injection (DI) failures. Instead of mapping the full dependency chain upfront, it executed sequential build-run-fail cycles (IMapper $\rightarrow$ IDateTimeProvider $\rightarrow$ ILoggerService), significantly increasing token consumption and latency.

## Decision
We are implementing a dual-pronged standard for agent reliability and process efficiency.

### 1. Raw Output First Standard
Any assertion about the environment (file existence, build status, error traces) must be accompanied by the verbatim, unmodified output of the tool used.
- **General Agent:** Must return complete `stdout` and `stderr`. No truncation or summarization.
- **Explore Agent:** Must provide raw output of `glob`, `read`, or `grep`. Inferences are forbidden unless the tool output is explicitly empty.
- **Verification:** The Primary Orchestrator must cross-verify "negative" results with a direct tool call if raw evidence is missing.

### 2. Proactive Mapping Protocol
For all tasks involving dependency chains, service registrations, or architectural boundary shifts, a "Proactive Mapping" approach is mandated.
- **Analysis Mandate:** The `architect-developer` MUST invoke the `architect` agent to produce a dependency graph and registration order BEFORE delegating to the `developer`.
- **Sequence of Operations:** `Discovery` $\rightarrow$ `Architect (Dependency Graph)` $\rightarrow$ `Documenter (ADR)` $\rightarrow$ `Developer (Implementation)`.

## Justification
- **Elimination of Hallucinations:** Raw outputs allow the orchestrator to distinguish between tool failures, empty results, and model hallucinations.
- **Cycle Compression:** Reducing the number of build cycles from $N$ (per missing dependency) to 1 by identifying the entire chain in a single pass.
- **Auditability:** Session history becomes a verifiable record of truth rather than a series of interpretations.

## Consequences
- **Positive:** Higher reliability of reporting, faster time-to-resolution for infrastructure errors, and stronger adherence to the Governance Framework.
- **Negative:** Increased token consumption per response due to raw logs and slightly higher initial latency for architectural analysis.
- **Operational:** Requires updates to agent system prompts to forbid summarization and enforce proactive mapping.

## Metadata
- **Author:** Architect-Developer
- **Date:** 2026-05-11
- **Framework Alignment:** AGENTS.md, Session S20260505-001 Analysis
