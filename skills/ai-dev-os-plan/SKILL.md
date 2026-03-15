---
name: ai-dev-os-plan
description: |
  Creates an implementation plan with relevant AI Dev OS guideline checklists
  before writing any code. Analyzes the request, identifies affected files and
  applicable guidelines, then presents a plan with embedded checklist for user
  approval. Implementation begins only after approval.
argument-hint: "<implementation request>"
allowed-tools: Read, Grep, Glob, Bash, EnterPlanMode, ExitPlanMode
---

# AI Dev OS Implementation Plan

## Execution Flow

### 1. Enter Plan Mode
Enter plan mode immediately. All analysis happens before any code is written.

### 2. Analyze the Request
Parse the user's implementation request and identify:
- **Goal**: What needs to be built or changed
- **Scope**: New feature / bug fix / refactoring / enhancement
- **Affected area**: Which parts of the codebase are involved

### 3. Identify Affected Files
Search the codebase to determine:
- Files that will be **modified**
- Files that will be **created**
- Files that will be **deleted**
- Related files that provide context (imports, tests, configs)

### 4. Parse CLAUDE.md and Load Guidelines
Extract the list of guideline file paths from the project's CLAUDE.md.
Read the referenced guideline files to understand the active rules.

### 5. Build Dynamic Mapping
Map affected files to their relevant guidelines using file pattern matching.

Examples by tech stack:
- **Python**: `*.py` → code.md, naming.md, validation.md; `*/router.py` → security.md, cors.md; `*/models.py` → naming.md, validation.md; `*/schemas.py` → validation.md; `alembic/**` → naming.md
- **Next.js**: `*.tsx` → ui.md, form.md, code.md, naming.md; `app/**/page.tsx` → routing.md; `app/**/action.ts` → server-actions.md
- **Go**: `*.go` → code.md, naming.md, error-handling.md

If checklist templates exist in the plugin's `checklist-templates/` directory for the detected tech stack, load them as a reference.

### 6. Extract Relevant Checklist Items
From the mapped guidelines, extract items that are relevant to **this specific change**:
- Filter by keywords: "MUST", "MUST NOT", "PROHIBITED", "REQUIRED"
- If a guideline has `checklist: [...]` in frontmatter, use those items
- Discard items unrelated to the current scope (e.g., skip database rules if no DB changes)

### 7. Present the Plan
Output the plan in the following format:

```markdown
## Implementation Plan

### Goal
> [One-line summary of what will be implemented]

### Scope
- Type: [new feature / bug fix / refactoring / enhancement]
- Risk: [low / medium / high]

### Changes
| Action | File | Description |
|--------|------|-------------|
| modify | path/to/file.py | Add validation logic |
| create | path/to/new.py | New service module |
| ...    | ...              | ...                 |

### Implementation Steps
1. [Step 1 description]
2. [Step 2 description]
3. ...

### Guideline Checklist
> Auto-generated from AI Dev OS guidelines applicable to this change.

#### [guideline-file-1.md]
- [ ] Rule 1
- [ ] Rule 2

#### [guideline-file-2.md]
- [ ] Rule 3
- [ ] Rule 4

### Principles to Keep in Mind
> From L2 principles relevant to this change.
- [Principle 1]: [Brief explanation of relevance]
- [Principle 2]: [Brief explanation of relevance]
```

### 8. Wait for Approval
Ask the user to review and approve the plan. Accept one of:
- **Approve**: Proceed with implementation
- **Modify**: User requests changes to the plan → update and re-present
- **Cancel**: Abort without making any changes

### 9. Exit Plan Mode and Implement
Upon approval:
1. Exit plan mode
2. Implement each step from the plan
3. After each file change, mentally check off the relevant guideline items
4. After all changes are complete, output a summary showing checklist completion:

```markdown
## Checklist Completion

- [x] Rule 1 — applied in file.py:42
- [x] Rule 2 — applied in file.py:58
- [ ] Rule 3 — N/A (no database changes in this scope)
```

### 10. Suggest Follow-up
Recommend the user run `/ai-dev-os-check` for a full compliance verification before committing.
