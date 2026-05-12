# Inventario de Modelos Disponibles No Utilizados (Mayo 2026)

Este documento registra los modelos identificados en el ecosistema de OpenCode que han sido descartados o puestos en reserva para la configuración actual de agentes.

## 1. Modelos en Reserva Estratégica (Tier 1 Backup)
Modelos de alta capacidad que están listos para ser activados si los modelos principales fallan o presentan latencia excesiva.

| Modelo | Razón de Reserva | Alternativa Activa |
| :--- | :--- | :--- |
| **DeepSeek v4 Pro (Max)** | Reservado como backup de alta fidelidad. Superado ligeramente por Gemini 3.1 Pro en razonamiento abstracto. | `gemini-3.1-pro-preview` |
| **Gemma 4 (31B Dense)** | Reservado para tareas de razonamiento local si falla la conexión cloud. | `qwen3-coder-next` |

## 2. Modelos Descartados por Rendimiento Subóptimo
Modelos que no ofrecen la mejor relación rendimiento/coste frente a las opciones seleccionadas.

| Modelo | Razón de Exclusión | Alternativa Asignada |
| :--- | :--- | :--- |
| **Gemini 3 Flash** | Se prefiere la versión **Pro** para arquitectura y las versiones **Lite** para volumen. | `gemini-3.1-pro-preview` |
| **Gemini 2.5 Flash** | Cuota limitada. Se prefiere usar las versiones **Lite** (100% cuota) para tareas de volumen. | `gemini-2.5-flash-lite` |
| **Qwen 3 Coder (Base)** | Superado por la versión `next` en benchmarks de ingeniería de software. | `qwen3-coder-next` |
| **Qwen 3.5 (397B)** | Excesivamente pesado para las tareas actuales de los subagentes especializados. | `qwen3-coder-next` |

## 3. Modelos de Emergencia (Standby)
*   **Qwen 3 Coder 480b:** Reserva para fallos masivos en Ollama Cloud.
*   **Minimax (Variantes Pro):** Reservadas para cuando se agote la cuota de la versión gratuita (Zen).

---
*Este inventario se actualiza automáticamente según la estrategia definida en el ADR 002.*
