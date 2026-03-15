---
name: ai-dev-os-scan
description: |
  Scans ALL source files in the project against AI Dev OS guidelines.
  Unlike /ai-dev-os-check (which only checks git diff), this performs
  a full project-wide compliance scan. Use for initial audits, periodic
  full reviews, or after introducing new guidelines.
argument-hint: "[directory or glob pattern]"
allowed-tools: Read, Grep, Glob, Bash
context: fork
agent: general-purpose
---

# AI Dev OS Full Project Scan

## Execution Flow

### 1. Parse CLAUDE.md
Extract the list of guideline file paths from CLAUDE.md.

### 2. Discover Target Files
If the user provides a directory or glob pattern, use that as the scope.
Otherwise, scan the entire project:
- Find all source files using `find` or `Glob` (e.g., `**/*.{ts,tsx,js,jsx,py,go}`)
- Exclude common non-source directories: `node_modules/`, `.git/`, `dist/`, `build/`, `.next/`, `__pycache__/`, `.venv/`, `vendor/`
- Exclude files listed in `.gitignore`

### 3. Build Dynamic Mapping
Dynamically build the mapping between file patterns and guidelines.

Examples by tech stack:
- **Python**: `*.py` → code.md, naming.md, validation.md; `*/router.py` → security.md, cors.md; `*/models.py` → naming.md, validation.md; `*/schemas.py` → validation.md; `alembic/**` → naming.md
- **Next.js**: `*.tsx` → ui.md, form.md, code.md, naming.md; `app/**/page.tsx` → routing.md; `app/**/action.ts` → server-actions.md
- **Go**: `*.go` → code.md, naming.md, error-handling.md

### 4. Extract Check Items
Auto-detect check items from each guideline using the following keywords:
- "MUST", "MUST NOT", "PROHIBITED", "REQUIRED", etc.
- If frontmatter contains `checklist: [...]`, use that instead

If checklist templates exist in the plugin's `checklist-templates/` directory for the detected tech stack, load them as a reference.

### 5. Run Checks (Batch Processing)
Process files in batches to manage context window:
- Group files by directory or module
- For each batch:
  - Auto-check items detectable via static analysis (Grep/Glob)
  - Flag items requiring judgment with ⚠️ for human review
- Track progress: "Scanning batch N/M..."

### 6. Aggregate Results
Combine results from all batches:
- Deduplicate violations
- Sort by severity (❌ violations first, then ⚠️ reviews)
- Group by guideline for pattern analysis

### 7. Report Output

Output in the following format:

```markdown
## AI Dev OS Full Project Scan Report

### Summary
- Total files scanned: N
- ✅ Passed: N / ⚠️ Review: N / ❌ Violation: N
- Compliance rate: N%

### Violations by Guideline
| Guideline | Violations | Files Affected | Most Common Issue |
|-----------|-----------|----------------|-------------------|

### Top Violations
| # | File | Line | Rule | Guideline | Principle |
|---|------|------|------|-----------|-----------|

### Review Required
| File | Line | Item | Guideline |
|------|------|------|-----------|

### Hot Spots (Files with Most Violations)
| File | Violations | Reviews | Top Issues |
|------|-----------|---------|------------|

### Recommendations
1. [Priority: High] ...
2. [Priority: Medium] ...

### Why These Rules?
> Explains the rationale for the most frequent violations, tracing back to L2 principles → L1 philosophy
```

## Options
- Default: Scan all source files in the project
- `[directory]`: Scan only files under the specified directory (e.g., `src/features/auth`)
- `[glob]`: Scan files matching the pattern (e.g., `**/*.tsx`)

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
