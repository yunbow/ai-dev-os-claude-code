---
name: ai-dev-os-check
description: |
  Checks whether code changes comply with AI Dev OS guidelines.
  Supports git diff (default), branch comparison, and staged changes.
  Run manually or automatically after coding or before commits.
  Auto-detects guideline files referenced from CLAUDE.md.
argument-hint: "[branch-name or --staged]"
allowed-tools: Read, Grep, Glob, Bash
---

# AI Dev OS Guideline Compliance Check

## Execution Flow

### 1. Determine Scope
Parse the argument to determine the check scope:
- **No argument**: Check unstaged changes (`git diff --name-only`)
- **`--staged`**: Check staged changes (`git diff --cached --name-only`)
- **`[branch]`** (e.g., `main`, `develop`): Check all changes vs. the specified branch (`git diff [branch]...HEAD --name-only`) — ideal for PR review

### 2. Parse CLAUDE.md
Extract the list of guideline file paths from CLAUDE.md.

### 3. Get Changed Files
Retrieve changed files based on the determined scope.
If no files are changed, report "No changes to check" and exit.

### 4. Build Dynamic Mapping
Dynamically build the mapping between file patterns and guidelines.

Examples by tech stack:
- **Python**: `*.py` → code.md, naming.md, validation.md; `*/router.py` → security.md, cors.md; `*/models.py` → naming.md, validation.md; `*/schemas.py` → validation.md; `alembic/**` → naming.md
- **Next.js**: `*.tsx` → ui.md, form.md, code.md, naming.md; `app/**/page.tsx` → routing.md; `app/**/action.ts` → server-actions.md
- **Go**: `*.go` → code.md, naming.md, error-handling.md

### 5. Extract Check Items
Auto-detect check items from each guideline using the following keywords:
- "MUST", "MUST NOT", "PROHIBITED", "REQUIRED", etc.
- If frontmatter contains `checklist: [...]`, use that instead

### 6. Run Checks
- Auto-check items detectable via static analysis (Grep/Glob)
- Flag items requiring judgment with ⚠️ for human review

### 7. Display Traceability
When violations are found, show traceability to L2 principles:
- "This rule is based on the principle 'Early Return'"
- "Derived from the philosophy 'Write code for the reader'"

### 8. Report Output

Output in the following format:

```markdown
## AI Dev OS Compliance Report

### Scope
- Mode: [unstaged / staged / branch comparison (vs. {branch})]
- Files checked: N

### Summary
- ✅ Passed: N / ⚠️ Review: N / ❌ Violation: N

### Violations
| File | Line | Rule | Guideline | Principle |
|------|------|------|-----------|-----------|

### Review Required
| File | Line | Item | Guideline |
|------|------|------|-----------|

### Why This Rule?
> Explains the rationale for rule violations, tracing back to L2 principles → L1 philosophy
```

## JSON Output Mode

When the user requests JSON output (e.g., `--json` or "output as JSON"), format the report as:

```json
{
  "summary": { "passed": 0, "review": 0, "violation": 0 },
  "violations": [
    { "file": "", "line": 0, "rule": "", "guideline": "", "severity": "error" }
  ],
  "reviews": [
    { "file": "", "line": 0, "item": "", "guideline": "" }
  ]
}
```

This enables CI/CD integration (e.g., GitHub Actions can parse the output and fail the build on violations).
