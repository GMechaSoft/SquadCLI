# ADR 002: Model Allocation Strategy (May 2026)

## Status
Accepted

## Context
Following the 2026 LLM benchmarks and the availability of multiple providers (DeepSeek, Google Gemini, Zen, and local models), we need a strategy to allocate the most suitable model for each agent role. The goal is to maximize reasoning for critical tasks, optimize cost using free quotas, and ensure system resilience through provider diversification.

## Decision
We have established a "Model Tiering" and "Provider Diversification" strategy for agent allocation.

### 1. Allocation Matrix

| Agent | Model | Provider | Variant | Reason |
| :--- | :--- | :--- | :--- | :--- |
| **Architect-Developer** | `google/gemini-3.1-pro-preview` | Google | **high** | Maximum reasoning (94.3%) for orchestration. |
| **Architect** | `google/gemini-3.1-pro-preview` | Google | **high** | Superior architectural analysis and auditing. |
| **Developer** | `qwen3-coder-next` | Ollama Cloud | developer | State-of-the-art for coding tasks. |
| **Documenter** | `minimax-m2.5-free` | Zen | low | High-quality Markdown generation at no cost. |
| **General** | `minimax-m2.5-free` | Zen | low | Versatile multi-step execution using Zen's quota. |
| **Investigator** | `gemini-2.5-flash-lite` | Google | low | Fast technical intelligence using 100% free quota. |
| **Explore** | `gemini-3.1-flash-lite` | Google | low | High-context (2M tokens) exploration at no cost. |
| **Prompt Optimizer** | `gemma-4-e4b` | LMStudio | assistant | Local, private, and zero-latency refinement. |
| **Quick Response** | `gemma-4-e4b` | LMStudio | assistant | Fast conceptual support without network overhead. |

## Justification

### A. Performance & Specialized Intelligence
*   **Gemini 3.1 Pro (High):** Replaced DeepSeek as the primary choice for high-reasoning roles due to its superior score in reasoning benchmarks (94.3%). We prioritize technical excellence, assuming quota resets as part of the operational cycle.
*   **Qwen 3 Coder Next:** Remains the choice for the developer role for its specialized focus on repository-level coding.

### B. Cost Efficiency & Quota Management
*   **Google Gemini (Lite versions):** Utilized for high-volume read tasks (Explore, Investigator) to leverage the 100% free daily quotas, which renew automatically.
*   **Gemini Pro Quota:** Recognized as a "one-time" resource for the free tier; exhaustion is persistent without a paid plan.
*   **DeepSeek Failover:** Formalized as the primary high-reasoning fallback for `Architect-Developer` and `Architect` once Gemini Pro is unavailable. We use the **`medium`** variant to balance performance and cost.
*   **Minimax (Zen):** Integrated as a primary resource for documentation and general tasks to preserve Google and DeepSeek quotas for more critical reasoning tasks.

### C. System Resilience (Multi-Provider)
*   The architecture is distributed across **DeepSeek**, **Google**, **Zen**, and **Local** environments. This prevents system-wide failure if a single provider's API or quota is unavailable.

## Consequences
*   **Positive:** Optimized operational costs, high-fidelity reasoning where needed, and increased system uptime.
*   **Operational:** Requires monitoring of multiple provider quotas and maintaining diverse agent configurations.

## Metadata
*   **Author:** Architect-Developer / Gemini CLI
*   **Date:** 2026-05-04
*   **Reference:** ADR 001 (Agent-Based Specialization)
