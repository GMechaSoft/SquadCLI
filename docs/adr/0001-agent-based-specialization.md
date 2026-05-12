# ADR 0001: Agent-Based Specialization vs. Skills

## Status
Accepted

## Context
The OpenCode ecosystem requires a scalable and cost-effective way to handle specialized tasks such as documentation, prompt optimization, and codebase investigation. We had to decide between implementing these as **Skills** (injected instructions) or **Subagents** (independent execution units).

## Decision
We have decided to implement specialized roles as **Subagents** instead of Skills. This ensures that the primary orchestrator (Architect-Developer) can delegate tasks to models that are more cost-efficient and specialized for each task.

## Justification

### 1. Cost Efficiency (Model Tiering)
*   **Orchestrator:** Requires a "large brain" (high-reasoning model) to plan and oversee the Atomic Cycle.
*   **Specialists:** Tasks like documentation (`documenter`) or prompt refinement (`prompt-optimizer`) do not require high-tier reasoning. By using subagents, we can target these tasks to **cheaper, faster models**, significantly reducing token costs. **Skills always run on the primary agent's model**, which would be economically inefficient for basic tasks.

### 2. Context Window Management
*   **Isolation:** Subagents operate in their own session. Extensive logs, file reads, or intermediate steps do not clutter the primary orchestrator's context window.
*   **Consolidation:** The orchestrator only receives the final, high-signal result (e.g., a finished ADR or a list of relevant files), preserving its memory for long-term planning.

### 3. Parallelism
*   Subagents can perform research or independent tasks without blocking the main thought process of the orchestrator, allowing for a more modular development flow.

## Comparison: Agents vs. Skills

| Feature | Subagents (Chosen) | Skills |
| :--- | :--- | :--- |
| **Model Selection** | **Agnostic & Independent.** Can use cheaper models for simple tasks. | **Fixed.** Must use the primary agent's model (expensive). |
| **Context Overhead** | **Low.** Only the result is returned to the main session. | **High.** Instructions and execution history fill the main session. |
| **Session Isolation** | **Total.** Perfect for high-volume tasks (search, batch editing). | **None.** Good for global rules or style guides. |
| **State Management** | **Independent.** Good for multi-step logic. | **Shared.** Good for immediate behavior modification. |

## Consequences
*   **Positive:** Significant reduction in operational costs and cleaner primary session history.
*   **Negative:** Requires maintaining separate configuration files (`.md` in `@agents/`) for each role.
*   **Instruction:** Any new specialized role that involves significant processing or can be done by a lower-tier model **must** be implemented as a subagent.

## Metadata
*   **Author:** Architect-Developer / Gemini CLI
*   **Date:** 2026-05-04
*   **Framework Alignment:** AGENTS.md (Atomic Cycle Enforcement)
