# AI Dev OS Plugin — Claude Code — Guía de operación

## Tabla de contenidos

1. [Requisitos previos](#1-requisitos-previos)
2. [Instalación](#2-instalación)
3. [Configuración inicial](#3-configuración-inicial)
4. [Flujo de trabajo diario](#4-flujo-de-trabajo-diario)
5. [Mantenimiento periódico](#5-mantenimiento-periódico)
6. [Incorporación del equipo](#6-incorporación-del-equipo)
7. [Política de idioma](#7-política-de-idioma)
8. [Integración CI](#8-integración-ci)
9. [Solución de problemas](#9-solución-de-problemas)

---

## 1. Requisitos previos

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0
- Archivos de capas de AI Dev OS (L1-L3) configurados en tu proyecto (plantillas: [TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python))

---

## 2. Instalación

### Opción A: Git Submodule (Recomendado)

Añade como submódulo para mantener las actualizaciones de AI Dev OS separadas de tu proyecto:

```bash
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

### Opción B: Clonación directa

```bash
git clone https://github.com/yunbow/ai-dev-os-plugin-claude-code.git
cp -r ai-dev-os-plugin-claude-code/skills/ .claude/skills/
cp -r ai-dev-os-plugin-claude-code/agents/ .claude/agents/
```

### Configuración de hooks

Copia las definiciones de hooks de `hooks/hooks.json` al `.claude/settings.json` de tu proyecto:

```json
{
  "permissions": { ... },
  "hooks": {
    "PreToolUse": [ ... ],
    "PostToolUse": [ ... ],
    "UserPromptSubmit": [ ... ]
  }
}
```

Consulta `hooks/hooks.json` para la configuración completa de hooks.

#### Guía de fusión de hooks

Si tu `.claude/settings.json` ya existe y contiene otras configuraciones, fusiona los hooks manualmente:

#### Opción A: Fusión manual

1. Abre `hooks/hooks.json` y tu `.claude/settings.json` lado a lado
2. Copia cada array (`PreToolUse`, `PostToolUse`, `UserPromptSubmit`) de `hooks/hooks.json`
3. Si tu `settings.json` ya tiene una clave `hooks`, **añade** las entradas a cada array existente
4. Si tu `settings.json` no tiene una clave `hooks`, añade el objeto `hooks` completo

**Verificación**: Después de fusionar, confirma que tu `settings.json` contiene los arrays `PreToolUse`, `PostToolUse` y `UserPromptSubmit` bajo la clave `hooks`.

---

## 3. Configuración inicial

### Ejecutar el asistente de configuración

```text
/ai-dev-os-init [tech-stack]
```

Ejemplo:

```text
/ai-dev-os-init nextjs
```

El asistente:

1. Preguntará sobre tu stack tecnológico, escala del proyecto y archivos de reglas existentes
2. Detectará e importará reglas existentes (`.cursorrules`, `CLAUDE.md`, `.eslintrc`)
3. Generará la estructura de directorios de 4 capas
4. Creará un `CLAUDE.md` con referencias a directrices y cascada de prioridades
5. Opcionalmente configurará el aislamiento con git submodule

### Verificar la configuración

Después de la inicialización, confirma la estructura:

```text
ai-dev-os/
├── 01_philosophy/
├── 02_decision-criteria/
├── 03_guidelines/
│   ├── common/
│   └── frameworks/
└── 04_ai-frames/
```

---

## 4. Flujo de trabajo diario

### 4.1 Planificación antes de la implementación

Para cambios importantes, crea primero un plan con directrices:

```text
/ai-dev-os-plan Añadir autenticación de usuario con JWT
```

Esto:

1. Analiza tu solicitud e identifica los archivos afectados
2. Mapea archivos a directrices relevantes de AI Dev OS
3. Genera una checklist de reglas aplicables
4. Presenta el plan para tu aprobación antes de escribir código

El hook también sugerirá `/ai-dev-os-plan` cuando detecte prompts relacionados con implementación.

### 4.2 Crear tickets en lugar de implementar

Cuando quieras diferir la implementación — por ejemplo, para asignación al equipo o gestión del backlog — crea un ticket:

```text
/ai-dev-os-ticket Añadir autenticación de usuario con JWT
```

Esto:

1. Realiza el mismo análisis que `/ai-dev-os-plan` (archivos afectados, mapeo de directrices, checklist)
2. Pregunta dónde crear el ticket (si no está configurado en CLAUDE.md):
   - **Archivo local**: guarda como `TICKET-001-add-user-auth.md` en el directorio especificado
   - **GitHub Issue**: crea un issue mediante `gh issue create`
3. Presenta una vista previa para tu aprobación
4. Crea el ticket — **sin escribir código**

Para preconfigurar el destino del ticket, añade a tu `CLAUDE.md` del proyecto:

```markdown
## Ticket Settings
- output: file
- path: docs/tickets/phase1
```

Para GitHub Issues:

```markdown
## Ticket Settings
- output: github-issue
```

### 4.3 Escribir código

Escribe código como de costumbre. Los hooks automáticamente:

- Verifican el cumplimiento de directrices en cada operación Write/Edit
- Advierten sobre violaciones de reglas de dependencia al editar archivos L1-L2
- Recuerdan ejecutar verificaciones de cumplimiento antes de hacer commit

### 4.4 Antes del commit

Ejecuta la verificación de cumplimiento:

```text
/ai-dev-os-check
```

Esto:

1. Analiza `CLAUDE.md` para encontrar directrices aplicables
2. Obtiene archivos modificados de `git diff`
3. Mapea archivos a directrices relevantes
4. Verifica el cumplimiento y genera un informe

### 4.5 Después de la revisión de código

Cuando corriges código generado por IA durante la revisión, extrae nuevas reglas:

```text
/ai-dev-os-extract [file-path]
```

Esto:

1. Analiza el diff para identificar *por qué* se realizaron los cambios
2. Genera candidatos de reglas en formato `MUST` / `MUST NOT`
3. Propone archivos de directrices objetivo y enlaces a principios L2
4. Añade reglas a las directrices tras la aprobación

### 4.6 Entender las reglas

Cuando un miembro del equipo cuestiona una regla:

```text
/ai-dev-os-why [rule-or-guideline]
```

Ejemplo:

```text
/ai-dev-os-why "¿por qué está prohibido el tipo any?"
```

---

## 5. Mantenimiento periódico

### Mensual: Auditoría de salud

```text
/ai-dev-os-audit
```

Verificaciones:
- Cumplimiento de reglas de dependencia (sin términos específicos de herramientas en L1, sin detalles de framework en L2)
- Frescura (L1: 5 años, L2: 3 años, L3: 12 meses, L4: 4 meses)
- Trazabilidad (enlaces L3→L2, reglas huérfanas)
- Cobertura (directrices para todos los patrones de archivos)
- Consistencia (sin reglas contradictorias)

### Trimestral: Evolución espiral SECI

```text
/ai-dev-os-evolve
```

Analiza commits recientes y patrones de revisión para proponer actualizaciones a la filosofía L1 y los principios L2. Esta es la "espiral del conocimiento" — extrayendo conocimiento tácito de la práctica diaria hacia principios explícitos.

### Semanal/Mensual: Informe de cumplimiento

```text
/ai-dev-os-report 1w
/ai-dev-os-report 1m
```

Genera un informe resumido adecuado para reuniones de equipo o actualizaciones para stakeholders.

---

## 6. Incorporación del equipo

### Para nuevos miembros

1. Leer `ai-dev-os/01_philosophy/core-values.md` (5 min)
2. Revisar `ai-dev-os/02_decision-criteria/` (10 min)
3. Ejecutar `/ai-dev-os-why` para cualquier regla que no esté clara
4. Comenzar a codificar — los hooks guiarán el cumplimiento automáticamente

### Para equipos que adoptan AI Dev OS

1. Ejecutar `/ai-dev-os-init` en el proyecto
2. Comenzar con la plantilla **starter** (solo L3)
3. Usar `/ai-dev-os-extract` después de cada sesión de revisión de código
4. Después de 2-4 semanas, ejecutar `/ai-dev-os-audit` para identificar brechas
5. Construir gradualmente L1-L2 a través de `/ai-dev-os-evolve`

---

## 7. Política de idioma

Todos los archivos fuente del plugin (skills, agents, hooks, templates) se mantienen en **inglés**. Esto asegura la precisión óptima del LLM y una amplia accesibilidad. La documentación traducida se proporciona bajo `docs/i18n/` como referencia, pero el inglés es la única fuente de verdad (Single Source of Truth).

---

## 8. Integración CI

Los hooks proporcionan orientación en tiempo real durante el desarrollo, pero solo operan dentro de la herramienta de codificación AI. Para la aplicación a nivel de equipo, integra las verificaciones de AI Dev OS en tu pipeline de CI.

### Niveles de aplicación

| Nivel | Comportamiento | Recomendado para |
|-------|---------------|------------------|
| **Consultivo** | Hooks advierten durante desarrollo, CI reporta violaciones sin bloquear | Desarrolladores individuales, equipos pequeños |
| **Advertencia** | CI publica comentarios de violación en PRs pero permite merge | Equipos adoptando AI Dev OS gradualmente |
| **Bloqueo** | CI falla en violaciones críticas (seguridad, autenticación) | Equipos establecidos con reglas maduras |

Comienza con nivel consultivo y escala a medida que tus reglas maduren (`[draft]` → `[proven]`).

---

## 9. Solución de problemas

### Los hooks no se activan

- Verificar que los hooks estén en `.claude/settings.json` (no directamente en `hooks/hooks.json`)
- Comprobar que `jq` esté instalado (`jq --version`)
- En Windows, asegurarse de que bash esté disponible en PATH

### Skills no encontrados

- Verificar que los archivos de skills estén en el directorio `.claude/skills/`
- Cada skill debe tener un archivo `SKILL.md` con YAML frontmatter válido
- Ejecutar `ls .claude/skills/` para confirmar

### Falsos positivos en violaciones

- Si una verificación es demasiado estricta, editar la directriz correspondiente en `03_guidelines/`
- Usar `/ai-dev-os-why` para entender el fundamento de la regla antes de eliminarla
- Considerar relajar la regla en lugar de eliminarla por completo

### Rendimiento

- Las verificaciones de hooks se ejecutan en cada Write/Edit — si es lento, reducir el alcance de las directrices
- Usar skills con `context: fork` (extract, audit, evolve) para operaciones pesadas
- Los subagentes se ejecutan en contexto aislado para no contaminar la sesión principal

---

Languages: [English](../../operation-guide.md) | [日本語](../ja/operation-guide.md) | [简体中文](../zh-CN/operation-guide.md) | [한국어](../ko/operation-guide.md) | Español
