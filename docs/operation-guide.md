# AI Dev OS Plugin — Claude Code — Operation Guide

English | [日本語](./i18n/ja/operation-guide.md) | [简体中文](./i18n/zh-CN/operation-guide.md) | [한국어](./i18n/ko/operation-guide.md) | [Español](./i18n/es/operation-guide.md)

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Installation](#2-installation)
3. [Initial Setup](#3-initial-setup)
4. [Daily Workflow](#4-daily-workflow)
5. [Periodic Maintenance](#5-periodic-maintenance)
6. [Team Onboarding](#6-team-onboarding)
7. [Language Policy](#7-language-policy)
8. [CI Integration](#8-ci-integration)
9. [Troubleshooting](#9-troubleshooting)

---

## 1. Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0
- AI Dev OS layer files (L1-L3) set up in your project (see templates: [TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python))

---

## 2. Installation

### Option A: CLI (Recommended)

The fastest way to set up AI Dev OS with Claude Code:

```bash
npx ai-dev-os init --rules typescript --plugin claude-code
```

This automatically adds submodules, copies CLAUDE.md template, and merges hooks. See [AI Dev OS CLI](https://github.com/yunbow/ai-dev-os-cli) for all options.

### Option B: Git Submodule

Add as a submodule to keep AI Dev OS updates separate from your project:

```bash
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

### Option B: Direct Clone

```bash
git clone https://github.com/yunbow/ai-dev-os-plugin-claude-code.git
cp -r ai-dev-os-plugin-claude-code/skills/ .claude/skills/
cp -r ai-dev-os-plugin-claude-code/agents/ .claude/agents/
```

### Hook Setup

Copy the hook definitions from `hooks/hooks.json` into your project's `.claude/settings.json`:

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

Refer to `hooks/hooks.json` for the full hook configuration.

#### Hook Merge Guide

If your `.claude/settings.json` already exists and contains other settings, merge the hooks manually:

**Option A: Manual merge**

1. Open `hooks/hooks.json` and your `.claude/settings.json` side by side
2. Copy each array (`PreToolUse`, `PostToolUse`, `UserPromptSubmit`) from `hooks/hooks.json`
3. If your `settings.json` already has a `hooks` key, **append** the entries to each existing array
4. If your `settings.json` does not have a `hooks` key, add the entire `hooks` object

**Option B: Using jq (Unix/macOS)**

```bash
jq -s '.[0] * {hooks: (.[0].hooks // {} | to_entries + (.[1].hooks | to_entries) | group_by(.key) | map({(.[0].key): [.[] | .value[]] }) | add)}' .claude/settings.json hooks/hooks.json > .claude/settings.merged.json
mv .claude/settings.merged.json .claude/settings.json
```

**Verification**: After merging, confirm that your `settings.json` contains `PreToolUse`, `PostToolUse`, and `UserPromptSubmit` arrays under the `hooks` key.

---

## 3. Initial Setup

### Run the Setup Wizard

```
/ai-dev-os-init [tech-stack]
```

Example:
```
/ai-dev-os-init nextjs
```

The wizard will:
1. Ask about your tech stack, project scale, and existing rule files
2. Detect and import existing rules (`.cursorrules`, `CLAUDE.md`, `.eslintrc`)
3. Generate the 4-layer directory structure
4. Create a `CLAUDE.md` with guideline references and Specificity Cascade
5. Optionally set up git submodule isolation

### Verify Setup

After initialization, confirm the structure:

```
ai-dev-os/
├── 01_philosophy/
├── 02_decision-criteria/
├── 03_guidelines/
│   ├── common/
│   └── frameworks/
└── 04_ai-frames/
```

---

## 4. Daily Workflow

### 4.1 Planning Before Implementation

For non-trivial changes, create a guideline-aware plan first:

```
/ai-dev-os-plan Add user authentication with JWT
```

This will:
1. Analyze your request and identify affected files
2. Map files to relevant AI Dev OS guidelines
3. Generate a checklist of applicable rules
4. Present the plan for your approval before any code is written

The hook will also suggest `/ai-dev-os-plan` when it detects implementation-related prompts.

### 4.2 Creating Tickets Instead of Implementing

When you want to defer implementation — e.g., for team assignment or backlog management — create a ticket instead:

```
/ai-dev-os-ticket Add user authentication with JWT
```

This will:
1. Perform the same analysis as `/ai-dev-os-plan` (affected files, guideline mapping, checklist)
2. Ask where to create the ticket (if not configured in CLAUDE.md):
   - **Local file**: saves as `TICKET-001-add-user-auth.md` in the specified directory
   - **GitHub Issue**: creates an issue via `gh issue create`
3. Present a preview for your approval
4. Create the ticket — **without writing any code**

To pre-configure the ticket destination, add to your project's `CLAUDE.md`:

```markdown
## Ticket Settings
- output: file
- path: docs/tickets/phase1
```

Or for GitHub Issues:

```markdown
## Ticket Settings
- output: github-issue
```

### 4.3 Writing Code

Write code as usual. The hooks will automatically:
- Check guideline compliance on every Write/Edit operation
- Warn about dependency rule violations when editing L1-L2 files
- Remind you to run compliance checks before committing

### 4.4 Before Committing

Run the compliance check:

```
/ai-dev-os-check
```

This will:
1. Parse `CLAUDE.md` to find applicable guidelines
2. Get changed files from `git diff`
3. Map files to relevant guidelines
4. Check compliance and output a report

### 4.5 After Code Review

When you fix AI-generated code during review, extract new rules:

```
/ai-dev-os-extract [file-path]
```

This will:
1. Analyze the diff to identify *why* changes were made
2. Generate rule candidates in `MUST` / `MUST NOT` format
3. Propose target guideline files and L2 principle links
4. Add rules to guidelines upon approval

### 4.6 Understanding Rules

When a team member questions a rule:

```
/ai-dev-os-why [rule-or-guideline]
```

Example:
```
/ai-dev-os-why "why is any type prohibited?"
```

---

## 5. Periodic Maintenance

### Monthly: Health Audit

```
/ai-dev-os-audit
```

Checks:
- Dependency rule compliance (no tool-specific terms in L1, no framework details in L2)
- Freshness (L1: 5yr, L2: 3yr, L3: 12mo, L4: 4mo)
- Traceability (L3→L2 links, orphaned rules)
- Coverage (guidelines for all file patterns)
- Consistency (no contradicting rules)

### Quarterly: SECI Spiral Evolution

```
/ai-dev-os-evolve
```

Analyzes recent commits and review patterns to propose updates to L1 philosophy and L2 principles. This is the "knowledge spiral" — extracting tacit knowledge from daily practice into explicit principles.

### Weekly/Monthly: Compliance Report

```
/ai-dev-os-report 1w
/ai-dev-os-report 1m
```

Generates a summary report suitable for team meetings or stakeholder updates.

---

## 6. Team Onboarding

### For New Team Members

1. Read `ai-dev-os/01_philosophy/core-values.md` (5 min)
2. Skim `ai-dev-os/02_decision-criteria/` (10 min)
3. Run `/ai-dev-os-why` for any rules that seem unclear
4. Start coding — hooks will guide compliance automatically

### For Teams Adopting AI Dev OS

1. Run `/ai-dev-os-init` on the project
2. Start with the **starter** template (L3 only)
3. Use `/ai-dev-os-extract` after each code review session
4. After 2-4 weeks, run `/ai-dev-os-audit` to identify gaps
5. Gradually build L1-L2 through `/ai-dev-os-evolve`

---

## 7. Language Policy

All plugin source files (skills, agents, hooks, templates) are maintained in **English**. This ensures optimal LLM accuracy and broad accessibility. Translated documentation is provided under `docs/i18n/` for reference, but English remains the single source of truth.

---

## 8. CI Integration

Hooks provide real-time advisory guidance but operate only within the AI coding tool. They do not enforce compliance at the repository level. For team-wide enforcement, integrate AI Dev OS checks into your CI pipeline.

### GitHub Actions Example

```yaml
name: AI Dev OS Compliance
on: [pull_request]

jobs:
  compliance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive

      - name: Check guideline compliance
        run: |
          # Option 1: Run markdownlint on guideline files
          npx markdownlint-cli docs/ai-dev-os/**/*.md

          # Option 2: Custom compliance script
          # Parse ai-dev-os-scan JSON output and fail on violations
          # python scripts/check-compliance.py --strict
```

### Enforcement Levels

| Level | Behavior | Recommended For |
|-------|----------|-----------------|
| **Advisory** | Hooks warn during development, CI reports violations but does not block | Solo developers, small teams |
| **Warning** | CI posts violation comments on PRs but allows merge | Teams adopting AI Dev OS gradually |
| **Blocking** | CI fails on critical violations (security, auth) | Established teams with mature rules |

Start with advisory level and escalate as your rules mature (`[draft]` → `[proven]`).

---

## 9. Troubleshooting

### Hooks not triggering

- Verify hooks are in `.claude/settings.json` (not `hooks/hooks.json` directly)
- Check that `jq` is installed (`jq --version`)
- On Windows, ensure bash is available in PATH

### Skills not found

- Verify skill files are in `.claude/skills/` directory
- Each skill must have a `SKILL.md` file with valid YAML frontmatter
- Run `ls .claude/skills/` to confirm

### False positive violations

- If a check is too strict, edit the corresponding guideline in `03_guidelines/`
- Use `/ai-dev-os-why` to understand the rule's rationale before removing it
- Consider relaxing the rule rather than removing it entirely

### Performance

- Hook checks run on every Write/Edit — if too slow, reduce guideline scope
- Use `context: fork` skills (extract, audit, evolve) for heavy operations
- Sub-agents run in isolated context to avoid polluting your main session
