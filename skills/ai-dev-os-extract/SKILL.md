---
name: ai-dev-os-extract
description: |
  Extracts rules from the gap between AI-generated code and ideal code.
  Used after code review to add new rules to AI Dev OS guidelines.
  A Rule Harvesting approach to rule discovery.
argument-hint: "[file-path]"
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
context: fork
agent: general-purpose
---

# AI Dev OS Rule Harvesting

## Execution Flow

### 1. Get Target File Diff
Retrieve the diff between AI-generated code (before) and corrected code (after) using `git diff`.

### 2. Analyze the Diff
Infer "why it was changed" rather than "what changed".
Classify patterns:
- Naming convention violation → common/naming.md
- Security gap → common/security.md
- Architecture deviation → frameworks/*/overview.md

### 3. Generate Rule Candidates
- One-line rule format: "MUST: Always call auth() in server actions"
- Check for duplicates against existing guidelines

### 4. User Confirmation
- "Would you like to add the following rules?"
- Suggest the target file (L3 guideline) for each rule
- Suggest links to L2 principles

### 5. Append to Guideline Files
- Include traceability comments
- Record extraction date and rationale

## Output Example

```markdown
## Rule Extraction Results

### Detected Patterns (N rules extracted)

#### Rule 1: [Rule Name]
- **From**: [File name] (line number)
- **Pattern**: [Detected pattern]
- **Proposed Rule**: `MUST: [Rule content]`
- **Target**: [Target guideline file]
- **Traces to**: [L2 principle reference]

### Add these rules? [Y/n/edit]
```
