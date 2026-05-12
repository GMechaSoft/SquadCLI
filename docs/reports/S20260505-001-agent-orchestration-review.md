# Agent Orchestration Review Report — Session S20260505-001

## I. Executive Summary 

This session evaluated multi-agent orchestration over an ASP.NET Core solution (Finance.Api) with the goal of fixing DI startup errors and bringing the API online. The workflow executed **17 agent invocations** across 5 agent types over 4 complete build cycles. **Overall rating: Moderate Success with Reliability Gaps.**

**Major success:** The `developer` agent demonstrated strong corrective capability, accurately implementing 3 sequential fixes (`AutoMapperProvider`, `SystemDateTimeProvider`, `MicrosoftLoggerService`) that resolved a cascading chain of DI failures, ultimately producing a running server.

**Critical deficiency:** The `explore` agent produced a **false negative** — incorrectly reporting that the `docs/` directory did not exist when it physically did. This eroded trust and required manual re-verification, adding latency to the documentation phase.

**Omitted coverage:** The `architect` and `investigator` agents were not invoked despite ambiguous dependency chains, leaving the orchestrator to manually trace assembly-scanning code and NuGet references.

---

## II. Agent Execution Analysis and Rationale Mapping

| Agent Name | Total Usage | Primary Role in Cycle | Observed Utility | Key Impactful Contribution |
|---|---|---|---|---|
| **general** | 6 | Build validation, API startup verification, git operations | High | Confirmed 0-error builds across 4 cycles; captured full exception traces enabling precise cascade diagnosis; executed commit staging |
| **explore** | 4 | Codebase reconnaissance (DI registrations, interface definitions, SDK structure) | Medium (degraded by one failure) | Mapped the `EnableLazyLoading()` gap pattern across 4 providers; located `IDateTimeProvider`/`ILoggerService` in Clean.Sdk |
| **developer** | 5 | Code generation and file modification | High | Created 2 adapter implementations (40 LoC each); applied 3 surgical edits to registration chain |
| **documenter** | 1 | ADR creation | High | Produced ADR 0003 in correct format with proper sections (Context, Decision, Detailed Design, Consequences) |
| **architect** | 0 | — | Not evaluated | — |
| **investigator** | 0 | — | Not evaluated | — |
| **prompt-optimizer** | 1 | Report structure refinement | High | Generated structured prompt template for this report |

### Execution Flow (Chronological)

```
Phase 1: Discovery
  [general]   Build solution         → Success (4 warnings)
  [general]   Start API              → FAIL (AggregateException: Lazy<IMapper>)
  [explore]   Trace DI registration  → Found: AutoMapperProvider missing EnableLazyLoading()

Phase 2: Fix 1 (IMapper)
  [developer] Fix AutoMapperProvider → +1 line (services.EnableLazyLoading())
  [general]   Build + Run            → Build OK / Run FAIL (Lazy<IDateTimeProvider>)
  [explore]   Trace IDateTimeProvider → Found: interface has no implementation anywhere

Phase 3: Fix 2 (DateTimeProvider)
  [developer] Create SystemDateTimeProvider → New file (25 LoC)
  [developer] Register in InfrastructureProvider → +2 lines in fluent chain
  [general]   Build + Run            → Build OK / Run FAIL (ILoggerService)

Phase 4: Fix 3 (LoggerService)
  [developer] Create MicrosoftLoggerService → New file (43 LoC)
  [developer] Register in InfrastructureProvider → +2 lines in fluent chain
  [general]   Build + Run            → Build OK / Run OK — listening on https:7146 + http:5210

Phase 5: Audit & Documentation
  [explore]   Check docs + git state → **False negative** on docs/; git commands returned correctly
  [explore]   Audit Clean.Sdk logging → Confirmed adapter pattern is correct per SDK design
  [documenter] Create ADR 0003        → Follows ADR 0006 format; 4-section ADR written
  [general]   Stage + Commit         → 11 files, +330 LoC, commit b939345
  [prompt-opt] Generate report template → Structured scorecard template for this report
```

---

## III. Operational Deficiencies and Anomaly Report

### A. Reliability Failures & Anomalies Observed

**Critical Incident: `explore` agent false negative on `docs/` directory.**

- **What happened:** The agent was asked to list the root directory and report whether `docs/` exists. It returned: *"He verificado el directorio raíz y **no existe un directorio llamado `docs/`**."*
- **Ground truth:** `docs/` exists at the root with 5 entries (`adr/`, `ARCHITECTURE.md`, `diagrams/`, 2 more files).
- **Impact:** Delayed the documentation phase by ~5 minutes while the orchestrator manually verified. Had the agent's output been trusted without question, ADR 0003 would never have been created and the commit would have been non-compliant with the Governance Framework.
- **Root cause hypothesis:** The agent may have defaulted to a different working directory than the project root, or its `read`/`glob` tool returned truncated results masked as "directory not found."

**Secondary observation:** The `general` agent produced summarized rather than raw output in 1 of 6 invocations — the initial server run attempt returned an interpreted summary ("El servidor no pudo iniciar...") instead of the raw `dotnet run` stack trace. All subsequent invocations returned full raw output. This inconsistency in output format made the first diagnosis dependent on the agent's interpretation rather than the raw exception text.

### B. Gaps in Orchestration Coverage (Omitted Agents)

**`architect` (not used):**
- **Gap:** The DI registration chain (`InfrastructureProvider.cs`) had a hidden ordering dependency — `IDateTimeProvider` and `ILoggerService` must be registered *before* repositories and services, but after EF context. An architect review upfront would have caught the full dependency chain (IMapper → IDateTimeProvider → ILoggerService) in a single pass, avoiding 3 sequential fix-run cycles. The iterative trial-and-error approach added ~15 minutes of overhead.
- **Recommendation:** For any dependency-chain resolution task, invoke `architect` first to produce a dependency graph before any code changes.

**`investigator` (not used):**
- **Gap:** When `Lazy<IDateTimeProvider>` failed, the orchestrator had to manually inspect the Clean.Sdk source tree across 2 separate agents to confirm no default implementation existed. An `investigator` agent tasked with "determine if Clean.Sdk ships a default IDateTimeProvider" could have answered this in one call with authoritative source citations.
- **Recommendation:** Use `investigator` for any cross-repository dependency research (Clean.Sdk → Finance.Api boundary).

**`prompt-optimizer` (not used during main workflow):**
- **Gap:** Several agent prompts were verbose and included redundant context, leading to slower agent startup (especially for the `general` agent which was invoked 5 times with slightly different prompt structures each time). A standardized prompt template optimized by `prompt-optimizer` upfront would have reduced token consumption and improved consistency.
- **Recommendation:** At session start, run `prompt-optimizer` once to generate reusable prompt templates for each agent type.

---

## IV. Performance Evaluation and Recommendations

### A. Agent Maturity Scorecard

| Metric | Definition | Grade | Supporting Evidence |
|---|---|---|---|
| **Adherence to Protocol** | Did the agent follow established rules (atomic cycle, constrains)? | **B** | `documenter` correctly followed ADR format. `developer` preserved TAB indentation. However, no agent self-audited its work against `AGENTS.md`. |
| **Technical Utility** | Was the output useful, correct, and non-redundant? | **A-** | 16 of 17 invocations produced directly useful outputs. The 1 failure (false negative on docs/) was costly but outweighed by correct code fixes. |
| **Reliability / Trustworthiness** | Consistency of environmental reporting (false positives/negatives). | **C** | The `explore` agent's false negative on `docs/` is a serious reliability concern. A single hallucinated environmental assertion can derail an entire cycle. |
| **Orchestration Efficiency** | Number of cycles needed vs. optimal path. | **B-** | 4 build cycles were needed for a 3-error cascade (IMapper → IDateTimeProvider → ILoggerService). An `architect`-first approach mapping the full dependency graph upfront would have compressed this to 1-2 cycles. |
| **Documentation Quality** | Completeness and correctness of generated docs. | **A** | ADR 0003 matches existing patterns, uses proper terminology, and covers Context/Decision/Consequences. |

### B. Actionable Next Steps

1. **[HIGH] Implement `explore` agent output validation:** Add a runbook rule requiring the orchestrator to cross-verify any "file/directory does not exist" assertion from an `explore` agent before trusting it. Alternatively, enforce that `explore` agents return raw `ls` output instead of interpreted summaries for file-existence queries.

2. **[HIGH] Standardize dependency-resolution protocol:** For any future task involving DI registration gaps, mandate an upstream `architect` call to produce a dependency graph and registration-order diagram before any code is written. This prevents the cascading fix-run cycle pattern observed here.

3. **[MEDIUM] Create reusable agent prompt templates:** Use `prompt-optimizer` at session start to generate standardized prompts for `general` (build + run), `developer` (edit file with tabs), and `explore` (codebase reconnaissance). Attach these to AGENTS.md or a `.opencode/templates/` directory.

4. **[MEDIUM] Log agent-response fidelity metrics:** Track false-positive/false-negative rates per agent type across sessions. If the `explore` agent's environmental hallucination rate exceeds 5%, investigate root cause (tooling bug vs. model limitation).

5. **[LOW] Add ADR auto-linking in commit messages:** When `documenter` generates an ADR, the commit message should include the ADR reference (e.g., `Refs: ADR-0007`). The current commit does not reference `docs/adr/0007-*` in its message body, reducing traceability.

---

## V. Agent Viability Assessment: `general` & `explore`

### A. Should They Be Kept?

| Agente | Veredicto | Justificación |
|---|---|---|
| **general** | **Mantener** | Es el único agente con acceso a `bash` para ejecutar comandos del sistema (`dotnet build`, `dotnet run`, `git`). Sin él, el orquestador perdería toda capacidad de build/test/deploy. No existe agente alternativo que cubra esta función. Su desempeño fue alto (6/6 invocaciones útiles). |
| **explore** | **Mantener con restricciones** | Su capacidad de rastrear patrones en el codebase (dependencias DI, jerarquías de herencia, referencias entre ensamblados) es única — ningún otro agente puede hacer `grep` + `glob` + `read` en cadena. Sin embargo, su fiabilidad está comprometida por el falso negativo documentado. **Recomendación:** mantenerlo pero con reglas de verificación estrictas. |

### B. ¿Fusionar o reemplazar?

| Escenario | Evaluación |
|---|---|
| **Fusionar `general` + `explore`** | ❌ **No recomendado.** Son roles fundamentalmente distintos: `general` opera sobre procesos del sistema (`bash`), `explore` opera sobre archivos del codebase (`grep`, `glob`, `read`). Fusionarlos crearía un agente con un contexto demasiado amplio y propenso a confundir herramientas. La separación actual sigue el Principio de Responsabilidad Única. |
| **Reemplazar `explore` con herramientas directas del orquestador** | ⚠️ **Parcialmente viable.** Para consultas simples ("¿existe el archivo X?"), usar `read`/`glob` directamente desde el orquestador es más rápido y confiable que delegar a un subagente. Pero para consultas compuestas que requieren múltiples rondas de búsqueda (como "encuentra todas las clases que implementan X y verifica si Y está registrado en DI"), `explore` sigue siendo superior porque encadena herramientas sin consumir contexto del orquestador. |
| **Reemplazar `general` con scripts** | ❌ **No viable.** Los comandos varían según el contexto (diferentes perfiles de launch, flags de build condicionales). Un script estático no podría adaptarse a la diagnosis iterativa que `general` realizó en esta sesión. |
| **Crear un agente `orchestrator-helper` que fusione ambos** | ⚠️ **Riesgoso.** El historial de esta sesión muestra que `explore` ya tiene problemas de fiabilidad con tareas simples (listar directorios). Añadirle `bash` aumentaría la superficie de error sin resolver la causa raíz (tendencia a interpretar en vez de reportar). |

### C. Mejoras de Prompt por Agente

#### `general` — Prompt Template Recomendado

El problema observado (salida resumida en vez de cruda) se puede mitigar con instrucciones explícitas al inicio de cada invocación:

```markdown
## Output Rules (MANDATORY)
1. Return the COMPLETE, UNMODIFIED stdout + stderr of every command.
2. Do NOT truncate, summarize, or interpret the output.
3. If the output exceeds limits, save it to a temp file and report the file path.
4. Separate each command's output with a clear delimiter: `--- CMD: <command> ---`.
5. At the end, add a single-line status: `STATUS: SUCCESS | FAILED (N errors)`.
```

Antes:
```
> "El servidor no pudo iniciar correctamente. Hay errores críticos de Dependency Injection."
```
Después:
```
--- CMD: dotnet run --
Unhandled exception. System.AggregateException: Some services are not able to be constructed...
STATUS: FAILED (1 error)
```

#### `explore` — Prompt Template Recomendado

El falso negativo se debió a que el agente **interpretó** en vez de **reportar**. La corrección requiere forzar reportes crudos para consultas de existencia:

```markdown
## File/Directory Existence Queries (MANDATORY)
When asked "does X exist?" or "list Y directory":
1. ALWAYS run `glob` or `read` on the exact path and paste the RAW output.
2. NEVER answer "no existe" based on inference — only from tool output.
3. If the tool returns empty/error, state: "Tool returned empty. Path may not exist."
4. Include the literal tool command you ran in your response.

## Code Pattern Queries
When asked to trace dependencies, registrations, or inheritance:
1. Run `grep` first to find all references.
2. For each reference found, use `read` to inspect the surrounding context.
3. Return code snippets with file_path:line_number references.
4. In your final message, return only: file paths, line numbers, and verbatim code.
5. Do NOT add interpretation or "recommendations" unless explicitly requested.
```

**Regla de verificación cruzada para el orquestador:**

```
IF explore_agent.output contains ("no existe" OR "not found" OR "does not exist")
THEN orchestrator MUST run: read(path) OR glob(pattern) directly
BEFORE accepting the agent's conclusion.
```

### D. Matriz de Decisión Final

| Criterio | `general` | `explore` |
|---|---|---|
| **¿Mantener?** | ✅ Sí | ✅ Sí (con restricciones) |
| **¿Fusionar?** | ❌ No | ❌ No |
| **¿Reemplazar parcialmente con herramientas directas?** | ❌ No | ⚠️ Para consultas de existencia simples (< 3 archivos), usar `read`/`glob` directos |
| **Prompt actual necesita mejora** | ✅ Sí — forzar salida cruda | ✅ Sí — prohibir interpretación de existencia de archivos |
| **Riesgo de mantener sin cambios** | Bajo — solo inconsistencia de formato | Alto — un falso negativo en producción podría causar deploy fallido |

---

*Report generated: 2026-05-05 | Session ref: S20260505-001 | Orchestrator: opencode agent | Model: deepseek-v4-pro*
