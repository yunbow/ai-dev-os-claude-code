---
name: ai-dev-os-why
description: |
  Explains the "why" behind a specific AI Dev OS rule or guideline.
  Traces the path from L3 guideline → L2 principle → L1 philosophy,
  explaining the rationale and background of the rule.
  Used when team members question a rule.
argument-hint: "[rule-or-guideline]"
allowed-tools: Read, Grep, Glob
---

# AI Dev OS "Why This Rule?" Explainer

## Execution Flow

### 1. Parse the User's Question

Identify the relevant L3 guideline rule from the question.
(e.g., "Why is `any` prohibited?" → the `any`-related rule in common/code.md)

### 2. Load L3 Guideline

Retrieve the relevant rule and its context.

### 3. Reference L2 Principles

- Find the principle referenced by the guideline (via traceability links)
- If no link exists, infer from content

### 4. Reference L1 Philosophy

Identify the philosophical value that the principle is rooted in.

### 5. Provide Examples

- Concrete examples of what happens without this rule
- Before/After code snippets

## Output Format

```text
📖 Rule: [Rule content]
📁 Source: [Guideline file]

🔗 Traceability:
  L3 Guideline → [Specific rule]
  L2 Principle  → [Supporting principle]
  L1 Philosophy → [Underlying value]

💡 Without this rule:
  [Description of specific risks or problems]

📝 Before/After:
  [Code examples]
```

## Language

Respond in the same language as the project's CLAUDE.md file.
