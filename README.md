# AI Dev OS Plugin — Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

A Claude Code plugin that brings the **AI Dev OS** 4-layer model to life as executable Skills, Hooks, and Sub-agents.

**Part of [AI Dev OS](https://github.com/yunbow/ai-dev-os)** — a framework for turning tacit knowledge into enforceable AI coding rules ([Lifespan Layers](https://github.com/yunbow/ai-dev-os#lifespan-layers--the-4-layer-model)).
Requires [AI Dev OS Rules](https://github.com/yunbow/ai-dev-os-rules-typescript) set up in your project.

## Why This Plugin?

Turn AI Dev OS guidelines into **automated workflows** for Claude Code:

- **11 Skills** — `/ai-dev-os-check`, `/ai-dev-os-scan`, `/ai-dev-os-extract` and more
- **3 Sub-agents** — Philosophy advisor, principle checker, guideline auditor
- **Lifecycle Hooks** — Auto-check on code changes, pre-commit reminders
- **One command setup** — `npx ai-dev-os init --rules typescript --plugin claude-code`

![AI Dev OS Check & Fix Report](docs/images/ai-dev-os-check.png)

## Quick Start

```bash
npx ai-dev-os init --rules typescript --plugin claude-code
```

> Adds submodules, copies CLAUDE.md template, and merges hooks automatically.
> See [AI Dev OS CLI](https://github.com/yunbow/ai-dev-os-cli) for details.

Requires [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0 and AI Dev OS layer files (L1-L3) in your project ([TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python)).

<details>
<summary>Manual Setup</summary>

```bash
# 1. Add AI Dev OS rules as submodule
git submodule add https://github.com/yunbow/ai-dev-os-rules-typescript.git docs/ai-dev-os
# For Python projects:
# git submodule add https://github.com/yunbow/ai-dev-os-rules-python.git docs/ai-dev-os

# 2. Add this plugin as submodule
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

3. Copy the hooks from `hooks/hooks.json` into your project's `.claude/settings.json`
4. Run `/ai-dev-os-init` in **Claude Code chat** to set up the 4-layer structure
5. Start coding — hooks will guide you automatically

See [Operation Guide](./docs/operation-guide.md) for detailed instructions.

</details>

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| **Init** | `/ai-dev-os-init` | Setup wizard — introduces AI Dev OS to a project in 30 minutes |
| **Check** | `/ai-dev-os-check` | Checks code changes against guidelines (supports `git diff`, `--staged`, branch comparison) |
| **Scan** | `/ai-dev-os-scan` | Full project-wide compliance scan of ALL source files |
| **Review** | `/ai-dev-os-review` | Pre-PR self-review combining L3 compliance + L2 design review + L1 alignment |
| **Extract** | `/ai-dev-os-extract` | Extracts new rules from code review diffs (Rule Harvesting) |
| **Why** | `/ai-dev-os-why` | Traces a rule back through L3→L2→L1 to explain its rationale |
| **Plan** | `/ai-dev-os-plan` | Creates an implementation plan with guideline checklists before coding |
| **Ticket** | `/ai-dev-os-ticket` | Generates a ticket (local file or GitHub Issue) with implementation summary and checklist candidates |
| **Audit** | `/ai-dev-os-audit` | Audits 4-layer health: dependency rule, freshness, coverage, consistency |
| **Evolve** | `/ai-dev-os-evolve` | Analyzes recent commits to propose L1-L2 updates (SECI spiral) |
| **Report** | `/ai-dev-os-report` | Generates compliance summary for teams and stakeholders |

## Sub-agents

| Agent | Model | Role |
|-------|-------|------|
| `philosophy-advisor` | Opus | Architecture decisions based on L1 philosophy |
| `principle-checker` | Sonnet | Verifies code changes align with L2 principles |
| `guideline-auditor` | Sonnet | Audits L3 guideline coverage, consistency, and freshness |

## Hooks

| Event | Trigger | Action |
|-------|---------|--------|
| PreToolUse (Write/Edit) | Code changes | Lightweight guideline compliance check |
| PreToolUse (Bash: git commit) | Before commit | Reminder to run `/ai-dev-os-check` |
| PostToolUse (Write/Edit) | After L1-L2 file edits | Dependency rule violation warning |

<details>
<summary>Package Structure</summary>

```
ai-dev-os-plugin-claude-code/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── skills/
│   ├── ai-dev-os-init/          # Setup wizard
│   ├── ai-dev-os-check/         # Guideline compliance check (git diff)
│   ├── ai-dev-os-scan/          # Full project-wide compliance scan
│   ├── ai-dev-os-review/        # Pre-PR self-review (L1-L3)
│   ├── ai-dev-os-extract/       # Rule Harvesting from code
│   ├── ai-dev-os-why/           # Explain rule rationale (L3→L2→L1)
│   ├── ai-dev-os-audit/         # 4-layer health audit
│   ├── ai-dev-os-plan/           # Guideline-aware implementation planning
│   ├── ai-dev-os-ticket/        # Ticket generation with checklist candidates
│   ├── ai-dev-os-evolve/        # SECI spiral feedback (L4→L1)
│   └── ai-dev-os-report/        # Compliance report generation
├── agents/
│   ├── philosophy-advisor.md     # L1-based architecture judgment
│   ├── principle-checker.md      # L2 alignment verification
│   └── guideline-auditor.md      # L3 coverage & consistency audit
├── hooks/
│   └── hooks.json                # Lifecycle event hooks
├── templates/
│   ├── CLAUDE.md.template        # CLAUDE.md template with cascade
│   ├── ai-dev-os-starter/        # Minimal setup template
│   └── ai-dev-os-full/           # Full 4-layer template
└── settings.json                  # Default permission settings
```

</details>

## Specificity Cascade

When rules conflict: framework-specific > common > project-specific > decision criteria > philosophy. [→ Details](https://github.com/yunbow/ai-dev-os/blob/main/spec/priority-cascade.md)

## Related

| Repository | Description |
|---|---|
| [ai-dev-os](https://github.com/yunbow/ai-dev-os) | Framework specification and theory |
| [rules-typescript](https://github.com/yunbow/ai-dev-os-rules-typescript) | TypeScript / Next.js / Node.js guidelines |
| [rules-python](https://github.com/yunbow/ai-dev-os-rules-python) | Python / FastAPI guidelines |
| [plugin-kiro](https://github.com/yunbow/ai-dev-os-plugin-kiro) | Steering Rules and Hooks for Kiro |
| [plugin-cursor](https://github.com/yunbow/ai-dev-os-plugin-cursor) | Cursor Rules (.mdc) |
| [cli](https://github.com/yunbow/ai-dev-os-cli) | `npx ai-dev-os init` |
| [benchmark](https://github.com/yunbow/ai-dev-os-benchmark) | Quantitative benchmark — guideline impact data |

## License

[MIT](./LICENSE)

---

Languages: English | [日本語](docs/i18n/ja/README.md) | [简体中文](docs/i18n/zh-CN/README.md) | [한국어](docs/i18n/ko/README.md) | [Español](docs/i18n/es/README.md)
