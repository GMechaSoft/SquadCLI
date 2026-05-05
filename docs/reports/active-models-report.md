# Reporte de Estado de Modelos y Agentes (Mayo 2026)

Este reporte detalla la configuración activa de la infraestructura de agentes de OpenCode tras la optimización de alto rendimiento.

## 1. Configuración de Agentes Activos

| Agente | Modelo | Variante | Justificación |
| :--- | :--- | :--- | :--- |
| **Architect-Developer** | `google/gemini-3.1-pro-preview` | **high** | Máximo razonamiento (94.3%) para orquestación. |
| **Architect** | `google/gemini-3.1-pro-preview` | **high** | Auditoría de diseño de alta fidelidad. |
| **Developer** | `ollama-cloud/qwen3-coder-next` | developer | Especialista en código y lógica de terminal. |
| **Documenter** | `minimax-m2.5-free` | low | Generación de docs eficiente y gratuita. |
| **General** | `minimax-m2.5-free` | low | Amortiguador de cuotas para tareas mixtas. |
| **Investigator** | `google/gemini-2.5-flash-lite` | low | Inteligencia técnica rápida (100% cuota). |
| **Explore** | `google/gemini-3.1-flash-lite` | low | Lectura masiva (2M tokens, 100% cuota). |
| **Prompt Optimizer** | `pc-lmstudio/gemma-4-e4b` | assistant | Privacidad local y latencia cero. |
| **Quick Response** | `pc-lmstudio/gemma-4-e4b` | assistant | Soporte conceptual inmediato local. |

## 2. Resumen de Cuotas y Disponibilidad

| Proveedor | Estado de Cuota | Estrategia |
| :--- | :--- | :--- |
| **Google Gemini Pro** | Variable (0% - Reset pend.) | **Prioridad Alta:** Se usa para decisiones críticas. |
| **Google Gemini Lite** | **100.0% Disponible** | **Prioridad Masiva:** Usado para lectura y búsqueda. |
| **Minimax (Zen)** | Disponible | **Prioridad Operativa:** Usado para escritura de docs. |
| **DeepSeek (Cloud)** | Disponible | **Reserva (Tier 1):** Backup para el orquestador. |
| **Ollama / Local** | Permanente | **Base:** Garantiza la operatividad del desarrollador. |

---
*Reporte generado bajo la directiva de Excelencia Técnica (ADR 002).*
