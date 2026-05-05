# OpenCode Agent Configurations

This repository contains the official specialized agent configurations and governance frameworks for **OpenCode**. It provides a standardized set of agents ensuring consistent behavior, high-quality code, and efficient orchestration under a strict technical framework.

## 🚀 Core Agents

The core suite of agents includes:

- **Architect-Developer:** The primary orchestrator and governance enforcer. **Does not perform direct execution (edit/bash: deny) to ensure strict delegation and oversight.**
- **Architect:** Specialized in system design and feasibility validation.
- **Developer:** Focuses on clean, idiomatic implementation based strictly on ADRs.
- **Documenter:** Maintains ADRs and technical contracts within the `docs/` folder.
- **Investigator:** Technical intelligence specialist for analyzing official sources and RFCs.
- **Prompt Optimizer:** Refines agent interactions for maximum efficiency. **Enabled for codebase context analysis (grep/glob: allow).**
- **Quick Response:** Handles rapid conceptual inquiries. **Read-only (edit: deny) to prevent accidental modifications.**
- **Explore:** Fast, read-only codebase exploration and pattern discovery.
- **General:** Multi-step task execution (research, maintenance). **Must adhere to the Atomic Cycle for any implementation.**

All agents follow the **Agent Governance Framework** (see `AGENTS.md`).

## 📂 Project Structure

```text
.
├── agents/             # Agent definition files
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

## 📦 Change Log

| Date | Change |
|------|--------|
| 2026-05-04 | **Governance Hardening:** Restricted `architect-developer` and `quick-response` permissions. |
| 2026-05-04 | **Model Agnosticism:** Removed model-specific instructions from agent prompts. |
| 2026-05-04 | **Atomic Cycle Enforcement:** Updated `general` agent to strictly follow the Design -> ADR -> Implementation flow. |
| 2026-05-04 | **Optimizer Context:** Enabled `grep` and `glob` for `prompt-optimizer`. |
| 2026-05-04 | Updated `agents/architect.md` — model changed from `deepseek-v4-pro` to `deepseek-v4-flash`. |
| 2026-05-04 | Created `agents/explore.md` and `agents/general.md`. |

---
*Official OpenCode Configuration Repository.*
