---
name: ai-dev-os-ticket
description: |
  Creates a ticket (local file or GitHub Issue) containing an implementation
  summary and guideline checklist candidates. Analyzes the request the same way
  as /ai-dev-os-plan but outputs a ticket instead of executing the implementation.
  The ticket output destination follows the project's Ticket Settings in CLAUDE.md.
argument-hint: "<implementation request>"
allowed-tools: Read, Grep, Glob, Bash, Write, EnterPlanMode, ExitPlanMode
---

# AI Dev OS Ticket Generator

## Execution Flow

### 1. Enter Plan Mode

Enter plan mode immediately. All analysis happens before any output is produced.

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

### 7. Determine Ticket Output Destination

Look for a `## Ticket Settings` section in the project's CLAUDE.md.

**If found**, parse the settings:

```markdown
## Ticket Settings
- output: file                      # "file" or "github-issue"
- path: docs/tickets/phase1         # Required when output=file
```

**If NOT found**, ask the user:

> Where should the ticket be created?
> 1. **Local file** — specify the directory path (e.g., `docs/tickets/phase1`)
> 2. **GitHub Issue** — creates an issue via `gh issue create`

Wait for the user's answer before proceeding.

### 8. Present the Ticket Preview

Before creating the ticket, show a preview and ask for approval.

Output the preview in the following format:

```markdown
## Ticket Preview

### Title
> [TICKET-{NNN}-{slug}] [One-line summary]

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

### Guideline Checklist Candidates
> Auto-generated from AI Dev OS guidelines applicable to this change.
> The implementer MUST check each item during and after implementation.

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

Ask the user to review:

- **Approve**: Create the ticket
- **Modify**: User requests changes → update and re-present
- **Cancel**: Abort without creating anything

### 9. Create the Ticket

#### 9a. Local File (`output: file`)

**Numbering**: Scan the target directory for existing `TICKET-*.md` files, find the highest number, and increment by 1. If no tickets exist, start at `001`.

**Slug**: Derive from the goal — lowercase, hyphens, max 40 characters. Example: `add-user-validation`.

**File path**: `{path}/TICKET-{NNN}-{slug}.md`

Write the ticket content using the approved preview format. Wrap the entire content in the following file structure:

```markdown
---
title: "[One-line summary]"
type: [new feature / bug fix / refactoring / enhancement]
risk: [low / medium / high]
status: open
created: [YYYY-MM-DD]
---

[Approved preview content here — Goal, Scope, Changes, Steps, Checklist, Principles]
```

Report the created file path to the user.

#### 9b. GitHub Issue (`output: github-issue`)

Create a GitHub Issue using:

```bash
gh issue create --title "TICKET-{NNN}-{slug}: [One-line summary]" --body "..."
```

The body is the approved preview content (Goal through Principles).

**Numbering**: Use the GitHub Issue number assigned automatically. The `TICKET-{NNN}` prefix in the title uses sequential numbering based on existing issues with the `TICKET-` prefix, or starts at `001`.

Report the created issue URL to the user.

### 10. Exit Plan Mode

Exit plan mode after the ticket is created. Do NOT proceed to implementation.

### 11. Suggest Follow-up

Inform the user:
> Ticket created. When ready to implement, use `/ai-dev-os-plan` with this ticket as context,
> or assign it to a team member / future session.
