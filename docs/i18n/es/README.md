# AI Dev OS Plugin — Claude Code

[![Lint & Link Check](https://github.com/yunbow/ai-dev-os-plugin-claude-code/actions/workflows/lint.yml/badge.svg)](https://github.com/yunbow/ai-dev-os-plugin-claude-code/actions/workflows/lint.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](../../../LICENSE)

Un plugin de Claude Code que convierte el modelo de 4 capas de **AI Dev OS** en Skills, Hooks y Sub-agents ejecutables.

**Parte de [AI Dev OS](https://github.com/yunbow/ai-dev-os)** — un framework para convertir conocimiento tacito en reglas de codificacion AI aplicables ([Lifespan Layers](https://github.com/yunbow/ai-dev-os#lifespan-layers--the-4-layer-model)).
Requiere [AI Dev OS Rules](https://github.com/yunbow/ai-dev-os-rules-typescript) configurado en tu proyecto.

## ¿Por qué este plugin?

Convierte las directrices de AI Dev OS en **flujos de trabajo automatizados** para Claude Code:

- **11 Skills** — `/ai-dev-os-check`, `/ai-dev-os-scan`, `/ai-dev-os-extract` y mas
- **3 Sub-agents** — Asesor de filosofia, verificador de principios, auditor de directrices
- **Lifecycle Hooks** — Verificacion automatica en cambios de codigo, recordatorios pre-commit
- **Un solo comando** — `npx ai-dev-os init --rules typescript --plugin claude-code`

![AI Dev OS Check & Fix Report](../../../docs/images/ai-dev-os-check.png)

## Inicio rapido

```bash
npx ai-dev-os init --rules typescript --plugin claude-code
```

> Agrega submodules, copia la plantilla CLAUDE.md y fusiona hooks automaticamente.
> Consulta [AI Dev OS CLI](https://github.com/yunbow/ai-dev-os-cli) para mas detalles.

Requiere [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0 y archivos de capas AI Dev OS (L1-L3) en tu proyecto ([TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python)).

<details>
<summary>Configuracion manual</summary>

```bash
# 1. Agregar reglas AI Dev OS como submodule
git submodule add https://github.com/yunbow/ai-dev-os-rules-typescript.git docs/ai-dev-os
# Para proyectos Python:
# git submodule add https://github.com/yunbow/ai-dev-os-rules-python.git docs/ai-dev-os

# 2. Agregar este plugin como submodule
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

1. Copia los hooks de `hooks/hooks.json` al `.claude/settings.json` de tu proyecto
2. Ejecuta `/ai-dev-os-init` en el **chat de Claude Code** para configurar la estructura de 4 capas
3. Comienza a codificar — los hooks te guiaran automaticamente

Consulta la [Guia de operacion](./docs/operation-guide.md) para instrucciones detalladas.

</details>

## Skills (Habilidades)

| Habilidad | Comando | Descripcion |
|-----------|---------|-------------|
| **Init** | `/ai-dev-os-init` | Asistente de configuracion — introduce AI Dev OS en un proyecto en 30 minutos |
| **Check** | `/ai-dev-os-check` | Verificacion de cumplimiento de directrices (soporta `git diff`, `--staged`, comparacion de ramas) |
| **Scan** | `/ai-dev-os-scan` | Escaneo completo de cumplimiento de TODOS los archivos fuente del proyecto |
| **Review** | `/ai-dev-os-review` | Auto-revision pre-PR combinando cumplimiento L3 + revision de diseno L2 + alineacion L1 |
| **Extract** | `/ai-dev-os-extract` | Extrae nuevas reglas de diffs de revision de codigo (Rule Harvesting) |
| **Why** | `/ai-dev-os-why` | Rastrea una regla a traves de L3→L2→L1 para explicar su fundamento |
| **Plan** | `/ai-dev-os-plan` | Crea un plan de implementacion con checklists de directrices antes de codificar |
| **Ticket** | `/ai-dev-os-ticket` | Genera un ticket (archivo local o GitHub Issue) con resumen de implementacion y checklist candidato |
| **Audit** | `/ai-dev-os-audit` | Audita la salud de 4 capas: reglas de dependencia, frescura, cobertura, consistencia |
| **Evolve** | `/ai-dev-os-evolve` | Analiza commits recientes para proponer actualizaciones L1-L2 (espiral SECI) |
| **Report** | `/ai-dev-os-report` | Genera un resumen de cumplimiento para equipos y stakeholders |

## Sub-agents (Subagentes)

| Agente | Modelo | Rol |
|--------|--------|-----|
| `philosophy-advisor` | Opus | Decisiones arquitectonicas basadas en filosofia L1 |
| `principle-checker` | Sonnet | Verifica que los cambios de codigo se alineen con principios L2 |
| `guideline-auditor` | Sonnet | Audita cobertura, consistencia y frescura de directrices L3 |

## Hooks (Ganchos)

| Evento | Disparador | Accion |
|--------|------------|--------|
| PreToolUse (Write/Edit) | Cambios de codigo | Verificacion ligera de cumplimiento de directrices |
| PreToolUse (Bash: git commit) | Antes del commit | Recordatorio de ejecutar `/ai-dev-os-check` |
| PostToolUse (Write/Edit) | Despues de editar archivos L1-L2 | Advertencia de violacion de regla de dependencia |

<details>
<summary>Estructura del paquete</summary>

```text
ai-dev-os-plugin-claude-code/
├── .claude-plugin/
│   └── plugin.json              # Manifiesto del plugin
├── skills/
│   ├── ai-dev-os-init/          # Asistente de configuracion
│   ├── ai-dev-os-check/         # Verificacion de cumplimiento (git diff)
│   ├── ai-dev-os-scan/          # Escaneo completo de cumplimiento
│   ├── ai-dev-os-review/        # Auto-revision pre-PR (L1-L3)
│   ├── ai-dev-os-extract/       # Rule Harvesting
│   ├── ai-dev-os-why/           # Explicacion de fundamentos (L3→L2→L1)
│   ├── ai-dev-os-audit/         # Auditoria de salud de 4 capas
│   ├── ai-dev-os-plan/           # Planificacion de implementacion con directrices
│   ├── ai-dev-os-ticket/        # Generacion de tickets (resumen + checklist candidato)
│   ├── ai-dev-os-evolve/        # Retroalimentacion espiral SECI (L4→L1)
│   └── ai-dev-os-report/        # Generacion de informes de cumplimiento
├── agents/
│   ├── philosophy-advisor.md     # Juicio arquitectonico basado en L1
│   ├── principle-checker.md      # Verificacion de alineacion con L2
│   └── guideline-auditor.md      # Auditoria de directrices L3
├── hooks/
│   └── hooks.json                # Hooks de eventos del ciclo de vida
├── templates/
│   ├── CLAUDE.md.template        # Plantilla CLAUDE.md
│   ├── ai-dev-os-starter/        # Plantilla de configuracion minima
│   └── ai-dev-os-full/           # Plantilla completa de 4 capas
└── settings.json                  # Configuracion de permisos por defecto
```

</details>

## Cascada de prioridades

Cuando las reglas entran en conflicto: especifico del framework > comun > especifico del proyecto > criterios de decision > filosofia. [→ Detalles](https://github.com/yunbow/ai-dev-os/blob/main/spec/priority-cascade.md)

## Relacionados

| Repositorio | Descripcion |
|---|---|
| [ai-dev-os](https://github.com/yunbow/ai-dev-os) | Framework specification and theory |
| [rules-typescript](https://github.com/yunbow/ai-dev-os-rules-typescript) | TypeScript / Next.js / Node.js guidelines |
| [rules-python](https://github.com/yunbow/ai-dev-os-rules-python) | Python / FastAPI guidelines |
| [plugin-kiro](https://github.com/yunbow/ai-dev-os-plugin-kiro) | Steering Rules and Hooks for Kiro |
| [plugin-cursor](https://github.com/yunbow/ai-dev-os-plugin-cursor) | Cursor Rules (.mdc) |
| [cli](https://github.com/yunbow/ai-dev-os-cli) | `npx ai-dev-os init` |
| [benchmark](https://github.com/yunbow/ai-dev-os-benchmark) | Quantitative benchmark — guideline impact data |

## Licencia

[MIT](../../../LICENSE)

---

Languages: [English](../../../README.md) | [日本語](../ja/README.md) | [简体中文](../zh-CN/README.md) | [한국어](../ko/README.md) | Español
