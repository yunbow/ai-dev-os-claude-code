---
name: ai-dev-os-check
description: |
  Checks code changes against AI Dev OS guidelines and automatically fixes violations.
  Based on benchmark Test 011: check+fix achieves +9.9 improvement vs report-only (+0.8).
  Supports git diff (default), branch comparison, and staged changes.
  Use --dry-run for report-only mode (no fixes applied).
argument-hint: "[branch-name or --staged] [--dry-run]"
allowed-tools: Read, Grep, Glob, Bash, Edit, Write
---

# AI Dev OS Guideline Check & Fix

> **Design Principle**: Report-only checks have near-zero impact on code quality.
> Check + Fix in a single session achieves +9.9 improvement (benchmark Test 011).

## Execution Flow

### 1. Determine Scope & Mode

Parse the argument to determine the check scope and mode:

**Scope:**

- **No argument**: Check unstaged changes (`git diff --name-only`)
- **`--staged`**: Check staged changes (`git diff --cached --name-only`)
- **`[branch]`** (e.g., `main`, `develop`): Check all changes vs. the specified branch (`git diff [branch]...HEAD --name-only`) — ideal for PR review

**Mode:**

- **Default**: Check & Fix — find violations and fix them automatically
- **`--dry-run`**: Report only — list violations without modifying code

### 2. Parse CLAUDE.md

Extract the list of guideline file paths from CLAUDE.md.

### 3. Get Changed Files

Retrieve changed files based on the determined scope.
If no files are changed, report "No changes to check" and exit.

### 4. Select Checklist

Auto-detect the tech stack from the project and load the appropriate checklist template:

- `checklist-templates/nextjs.md` — for Next.js / React projects
- `checklist-templates/python.md` — for Python / FastAPI projects
- `checklist-templates/go.md` — for Go projects

Also load relevant guideline files from CLAUDE.md to extract additional MUST/MUST NOT rules.

### 5. Check — Systematic Violation Scan

Review ALL changed files against the checklist items systematically:

1. Read each changed file
2. Check each checklist item against the actual code
3. Record violations with: file, line number, rule ID, description

**Important**: Check ALL items, not just obvious ones. Security and naming rules are easy to miss.

### 6. Fix — Auto-Correct Violations

**Skip this step if `--dry-run` is specified.**

For each violation found:

1. Fix the violation directly in the code using Edit/Write tools
2. Verify the fix doesn't break surrounding code
3. Mark the item as fixed

**Fix principles:**

- Minimal changes — only fix what violates the checklist
- Preserve existing code style and formatting
- If a fix requires architectural changes (not a simple edit), flag it as ⚠️ manual review instead of auto-fixing

### 7. Re-Verify

After all fixes are applied, re-scan the fixed files to confirm:

- All auto-fixed violations are resolved
- No new violations were introduced by the fixes

### 8. Report Output

Output in the following format:

```markdown
## AI Dev OS Check & Fix Report

### Scope
- Mode: [Check & Fix / Dry Run (report only)]
- Target: [unstaged / staged / branch comparison (vs. {branch})]
- Files checked: N

### Summary
- ✅ Passed: N / 🔧 Fixed: N / ⚠️ Manual Review: N

### Fixed Violations
| # | File | Line | Rule | What was fixed |
|---|------|------|------|----------------|

### Manual Review Required
| # | File | Line | Rule | Why manual review is needed |
|---|------|------|------|----------------------------|

### Checklist Coverage
- Items checked: N / N
- Pass rate: N% (after fixes)
```

**In `--dry-run` mode**, replace "Fixed Violations" with:

```markdown
### Violations Found
| # | File | Line | Rule | Description | Auto-fixable? |
|---|------|------|------|-------------|---------------|
```

## JSON Output Mode

When the user requests JSON output (e.g., `--json` or "output as JSON"), format the report as:

```json
{
  "mode": "check-and-fix",
  "summary": { "passed": 0, "fixed": 0, "manual_review": 0 },
  "fixed": [
    { "file": "", "line": 0, "rule": "", "description": "" }
  ],
  "manual_review": [
    { "file": "", "line": 0, "rule": "", "reason": "" }
  ]
}
```

This enables CI/CD integration (e.g., GitHub Actions can parse the output and fail the build on manual_review items).
