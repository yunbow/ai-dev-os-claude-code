---
name: ai-dev-os-evolve
description: |
  Supports the spiral evolution of AI Dev OS.
  Analyzes recent AI coding practices (commit history, review records)
  and generates update proposals for L1 philosophy and L2 principles.
  Used in monthly retrospectives or quarterly reviews.
allowed-tools: Read, Grep, Glob, Bash
context: fork
agent: general-purpose
---

# AI Dev OS Spiral Feedback (SECI Spiral)

## Execution Flow

### 1. Analyze Recent N Commits/PRs

- Aggregate past results from guideline-checker
- Identify frequently occurring violation patterns
- Also detect patterns where "AI performed surprisingly well"

### 2. Abstract Patterns

Derive principles from individual cases:

- "auth() forgotten 3 times" → Strengthen principle "Authentication should be explicit, not implicit"
- "AI writes zod schemas accurately" → Confirm L3 rule is effective

### 3. Propose L1-L2 Updates

- Candidates for new principles
- Candidates for rewording existing principles
- Values discovered at the philosophy level

### 4. Report + Present Diff

Ask "Would you like to apply the following updates?" and apply after approval.
