---
name: ai-dev-os-audit
description: |
  Audits the health of the AI Dev OS 4-layer structure.
  Detects dependency rule violations (upper layers referencing lower layers),
  expiration, and coverage gaps.
  Used for monthly or quarterly periodic maintenance.
allowed-tools: Read, Grep, Glob, Bash
context: fork
agent: general-purpose
---

# AI Dev OS 4-Layer Health Audit

## Audit Items

### 1. Dependency Rules (Clean Architecture Principle)

- Do L1 files contain tool-specific terms? ("Claude Code", "Cursor", "Next.js", etc.)
- Do L2 files contain framework-specific implementation details?
- Is the one-way dependency L1 → L2 → L3 → L4 maintained?

### 2. Expiration Check

- L1: Last updated more than 5 years ago?
- L2: Last updated more than 3 years ago?
- L3: Last updated more than 12 months ago?
- L4: Last updated more than 4 months ago?
- Use `git log` to get the last update date for each file

### 3. Traceability

- Does each L3 guideline have a link to an L2 principle?
- Does each L4 AI Frame have a reference to an L3 guideline?
- Are there orphaned rules (not linked to any principle)?

### 4. Coverage

- Do all files referenced in CLAUDE.md actually exist?
- Are there guidelines for the project's major file patterns?
- Are there mapped guidelines for each directory under src/?

### 5. Consistency

- Are there contradictory rules across multiple guidelines?
- Are naming conventions consistent across all files?

## Output Format

```markdown
## AI Dev OS Health Audit Report

### Score: N/100

### Layer Health
| Layer | Files | Coverage | Freshness | Dependencies | Score |
|-------|-------|----------|-----------|-------------|-------|

### Issues Found
| Severity | Issue | Location | Action |
|----------|-------|----------|--------|

### Recommendations
1. [Priority: High] ...
2. [Priority: Medium] ...
```
