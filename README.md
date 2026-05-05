# OpenCode Agent Configurations

This repository contains the official specialized agent configurations and governance frameworks for **OpenCode**. It provides a standardized set of agents ensuring consistent behavior, high-quality code, and efficient orchestration under a strict technical framework.

## 🚀 Core Agents

The core suite of agents includes:

- **Architect-Developer:** The primary orchestrator and governance enforcer.
- **Architect:** Specialized in system design and feasibility validation.
- **Developer:** Focuses on clean, idiomatic implementation.
- **Documenter:** Maintains ADRs and technical contracts.
- **Investigator:** Codebase analysis and root-cause investigation.
- **Prompt Optimizer:** Refines agent interactions for maximum efficiency.
- **Quick Response:** Handles rapid inquiries and small tasks.
- **Explore:** Fast, read-only codebase exploration and pattern discovery.
- **General:** Multi-step task execution (investigation, implementation, documentation).

All agents follow the **Agent Governance Framework** (see `AGENTS.md`), which mandates internal reasoning in Spanish and technical output (code/docs) in English.

## 📂 Project Structure

```text
.
├── agents/             # Agent definition files
├── AGENTS.md           # Governance Framework
├── opencode.json       # Tool and provider configuration
└── README.md           # This file
```

## 🛠️ Governance & Standards

- **User Conversation:** Spanish.
- **Internal Reasoning:** English.
- **Technical Output:** English (Code, Docs, Commits).
- **Architecture:** SOLID, DRY, and Clean Architecture principles.
- **Atomic Development Cycle:**
    1. **Design:** Feasibility and patterns.
    2. **Documentation:** ADRs and contracts.
    3. **Implementation:** Clean, high-performance code.

## 📋 Sub-agent Permission Matrix

| Agent | read | edit | bash | grep | glob | list | question | todowrite | webfetch | websearch | ext_dir | skill |
|-------|------|------|------|------|------|------|----------|-----------|----------|-----------|---------|-------|
| architect | allow | deny | deny | allow | allow | allow | allow | — | — | — | — | deny |
| explore | allow | deny | deny | allow | allow | allow | — | — | — | — | — | deny |
| developer | allow | allow | allow | allow | allow | allow | allow | allow | — | — | — | deny |
| documenter | allow | allow | ask | allow | allow | allow | — | — | — | — | — | deny |
| investigator | allow | deny | ask | — | — | — | — | — | allow | allow | allow | deny |
| general | allow | allow | allow | allow | allow | allow | allow | allow | — | — | — | deny |
| prompt-optimizer | allow | deny | deny | deny | deny | — | — | — | deny | deny | — | deny |

(`—` = inherits global default, `ext_dir` = external_directory)

## 📦 Change Log

| Date | Change |
|------|--------|
| 2026-05-04 | Created `agents/explore.md` — read-only codebase exploration with `deepseek-v4-flash` (medium) |
| 2026-05-04 | Created `agents/general.md` — multi-step execution with `minimax-m2.5-free` |
| 2026-05-04 | Updated `agents/architect.md` — model changed from `deepseek-v4-pro` to `deepseek-v4-flash`, variant set to `medium` |
| 2026-05-04 | Updated `agents/architect-developer.md` — added `explore` and `general` to task whitelist |
| 2026-05-04 | Updated all sub-agents — explicit permissions for `grep`, `glob`, `list`, `question`, `todowrite`, `skill`, `webfetch`, `websearch` |

---
*Official OpenCode Configuration Repository.*
