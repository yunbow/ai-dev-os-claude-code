---
name: ai-dev-os-review
description: |
  Performs a comprehensive self-review before creating a PR.
  Combines guideline compliance checking (L3) with design-level
  review (L2) and philosophical alignment (L1).
  Unlike /ai-dev-os-check which only checks rules, this also
  evaluates architecture decisions and code design quality.
argument-hint: "[base-branch (default: main)]"
allowed-tools: Read, Grep, Glob, Bash
context: fork
agent: general-purpose
---

# AI Dev OS Pre-PR Self-Review

## Execution Flow

### 1. Determine Base Branch
- Use the argument as the base branch (default: `main`)
- Get all changed files: `git diff [base]...HEAD --name-only`
- Get commit messages: `git log [base]...HEAD --oneline`

### 2. Parse CLAUDE.md
Extract the list of guideline file paths from CLAUDE.md.
Also load L1 philosophy and L2 principles files for design-level review.

### 3. L3 Guideline Compliance Check
Run the same checks as `/ai-dev-os-check [base-branch]`:
- Build file-to-guideline mapping
- Extract and verify check items
- Collect violations and review items

### 4. L2 Design Review
For each changed file, evaluate higher-level design concerns:

- **Single Responsibility**: Does each module/function have one clear purpose?
- **Dependency Direction**: Do dependencies flow correctly (inward, not outward)?
- **Separation of Concerns**: Is UI / business logic / data access properly separated?
- **Naming Intent**: Do names express domain concepts accurately?
- **Error Handling Strategy**: Is error handling consistent with L2 principles?
- **Testability**: Is the design easy to test?

### 5. L1 Philosophy Alignment
Check whether the overall change aligns with the project's core values:
- Does this change serve the stated philosophical goals?
- Are there trade-offs that conflict with core values?

### 6. PR Description Draft (Optional)
If the review passes, offer to draft a PR description:
- Summary of changes
- Design decisions and rationale
- Guideline compliance status

### 7. Report Output

```markdown
## AI Dev OS Pre-PR Review

### Overview
- Base branch: {branch}
- Commits: N
- Files changed: N

### L3 Guideline Compliance
- ✅ Passed: N / ⚠️ Review: N / ❌ Violation: N

### L2 Design Review
| Perspective | Status | Notes |
|-------------|--------|-------|
| Single Responsibility | ✅/⚠️/❌ | |
| Dependency Direction | ✅/⚠️/❌ | |
| Separation of Concerns | ✅/⚠️/❌ | |
| Naming Intent | ✅/⚠️/❌ | |
| Error Handling | ✅/⚠️/❌ | |
| Testability | ✅/⚠️/❌ | |

### L1 Philosophy Alignment
- [Assessment of overall alignment with core values]

### Action Items
1. [Must fix before PR] ...
2. [Should consider] ...
3. [Nice to have] ...

### PR Description Draft
> [Auto-generated if review passes]
```
