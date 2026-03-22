# Contributing to AI Dev OS Plugin — Claude Code

Thank you for your interest in contributing!

## How to Contribute

### Reporting Issues

- Use [GitHub Issues](https://github.com/yunbow/ai-dev-os-plugin-claude-code/issues)
- Specify which skill, hook, or agent is affected

### Pull Requests

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Commit with a descriptive message
5. Open a Pull Request

### What to Contribute

| Directory | What We Need | Guidelines |
|-----------|-------------|------------|
| `skills/` | Skill improvements, new skills | Follow SKILL.md format (YAML frontmatter + execution flow). Keep skills focused on one task. |
| `agents/` | Agent improvements | Follow agent .md format (YAML frontmatter + role description). |
| `hooks/` | Hook improvements, new event handlers | Test thoroughly — hooks run on every tool invocation. Avoid false positives. |
| `templates/` | Template improvements | CLAUDE.md template should follow Two-Tier Context Strategy (10-15 high-impact rules only). |
| `checklist-templates/` | New language checklists, improvements | Keep checklists actionable and verifiable. |

### Skill Development Guidelines

- Each skill lives in `skills/{name}/SKILL.md`
- Required frontmatter: `name`, `description`, `allowed-tools`
- Optional: `argument-hint`, `context`, `agent`
- Execution flow should be step-by-step with clear decision points
- Output format should use consistent markdown tables

### Hook Development Guidelines

- Hooks are defined in `hooks/hooks.json`
- Filter non-source files (node_modules, dist, images, etc.)
- Use `additionalContext` for suggestions, not blocking
- Test with both TypeScript and Python projects

### Cross-Plugin Feature Parity

When adding a new feature to this plugin, consider adding the equivalent to:

- [ai-dev-os-plugin-kiro](https://github.com/yunbow/ai-dev-os-plugin-kiro) (as steering rule)
- [ai-dev-os-plugin-cursor](https://github.com/yunbow/ai-dev-os-plugin-cursor) (as .mdc rule)

### Translation Guide

- Translations are in `docs/i18n/{lang}/`
- Currently supported: `ja`, `zh-CN`, `ko`, `es`

## Code of Conduct

Be respectful, constructive, and inclusive.

---

Languages: English | [日本語](docs/i18n/ja/CONTRIBUTING.md)
