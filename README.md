# OpenCode Agent Configurations

This repository contains the official specialized agent configurations and governance frameworks for **OpenCode**. It provides a standardized set of agents ensuring consistent behavior, high-quality code, and efficient orchestration under a strict technical framework.

## 🚀 Core Agents

The core suite of agents includes:

- **Architect-Developer:** The primary orchestrator and governance enforcer. **Powered by Gemini 3.1 Pro** for maximum reasoning. Restricted permissions (edit/bash: deny) to ensure strict oversight.
- **Architect:** Specialized in macro-architecture and design auditing. **Powered by Gemini 3.1 Pro** for high-fidelity technical evaluation.
- **Developer:** Technical executor focusing on clean, idiomatic implementation. **Powered by Qwen 3 Coder Next** for state-of-the-art coding logic.
- **Documenter:** Maintains ADRs and technical contracts using **Minimax (Zen)** to preserve premium quotas.
- **Investigator:** Technical intelligence specialist for analyzing official sources using **Gemini 2.5 Flash Lite** (100% free quota).
- **Prompt Optimizer:** Refines agent interactions locally via **Gemma 4**. Enabled for codebase context analysis (grep/glob: allow).
- **Quick Response:** Handles rapid conceptual inquiries locally via **Gemma 4**. Read-only (edit: deny).
- **Explore:** Fast codebase exploration using **Gemini 3.1 Flash Lite** (2M token window, 100% free quota).
- **General:** Multi-step task execution (research, maintenance) using **Minimax (Zen)** as a quota buffer.

All agents follow the **Agent Governance Framework** (see `AGENTS.md`).

## 📂 Project Structure

```text
.
├── agents/             # Agent definition files
├── docs/
│   ├── adr/            # Architectural Decision Records
│   └── reports/        # Infrastructure and model reports
├── AGENTS.md           # Governance Framework
├── opencode.json       # Tool and provider configuration
└── README.md           # This file
```

## 🛠️ Governance & Standards

- **User Conversation:** Spanish (Español).
- **Internal Reasoning:** English.
- **Technical Output:** English (Code, Docs, Commits).
- **Architecture:** SOLID, DRY, and Clean Architecture principles.
- **Atomic Development Cycle (Mandatory):**
    1. **Design:** Feasibility and patterns.
    2. **Documentation:** ADRs and contracts.
    3. **Implementation:** Clean, high-performance code based on the ADR.

## 📋 Sub-agent Permission Matrix

| Agent | read | edit | bash | grep | glob | list | question | webfetch | websearch | ext_dir | skill |
|-------|------|------|------|------|------|------|----------|----------|-----------|---------|-------|
| architect-developer | allow | **deny** | **deny** | allow | allow | allow | allow | — | — | — | deny |
| architect | allow | deny | deny | allow | allow | allow | allow | — | — | — | deny |
| explore | allow | deny | deny | allow | allow | allow | — | — | — | — | deny |
| developer | allow | allow | allow | allow | allow | allow | allow | — | — | — | deny |
| documenter | allow | allow | ask | allow | allow | allow | — | — | — | — | deny |
| investigator | allow | deny | ask | — | — | — | — | allow | allow | allow | deny |
| general | allow | allow | allow | allow | allow | allow | allow | — | — | — | deny |
| prompt-optimizer | allow | deny | deny | **allow** | **allow** | — | — | deny | deny | — | deny |
| quick-response | allow | **deny** | deny | — | — | — | — | — | — | — | deny |

(`—` = inherits global default, `ext_dir` = external_directory)

## 🔄 Model Failover Protocol (Quota Management)

When primary `Gemini Pro` quotas are exhausted, transition to the following secondary providers (using `variant: medium` for optimization):

- **Critical (Orchestration):** Switch `Architect-Developer` from `Gemini Pro` to `deepseek/deepseek-v4-pro` (`medium`).
- **Secondary (Auditing):** Switch `Architect` from `Gemini Pro` to `deepseek/deepseek-v4-flash` (`medium`).
- **General/Support:** Maintain standard free-quota providers (Minimax, Gemini Flash Lite) as primary.

*Always verify quota status before switching.*

---
*Official OpenCode Configuration Repository.*
