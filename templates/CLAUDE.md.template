# [Project Name] - Claude Code Guidelines

Write code for this project following AI DEV OS.
When there are conflicts, higher sections take priority.

<!--
  AI DEV OS Two-Tier Context Strategy (validated by benchmark):
  - This file (static context): 3-5 project-specific files only (~8K tokens)
  - Dynamic checks (/ai-dev-os-check): verify against ALL guidelines post-generation

  IMPORTANT: Do NOT uncomment all guideline files.
  Benchmark data shows loading too many guidelines DEGRADES AI output quality.
  Select only 3-5 files most relevant to your project.
  See: https://github.com/yunbow/ai-dev-os-benchmark
-->

## Development Guidelines

### 1. High-Impact Guidelines (select 3-5 total)

<!-- Recommended core set — uncomment the ones relevant to your project -->
- docs/ai-dev-os/03_guidelines/common/security.md
- docs/ai-dev-os/03_guidelines/common/validation.md
- docs/ai-dev-os/03_guidelines/frameworks/[stack]/project-structure.md

<!-- Optional — add only if your project needs these -->
# - docs/ai-dev-os/03_guidelines/common/error-handling.md
# - docs/ai-dev-os/03_guidelines/common/code.md
# - docs/ai-dev-os/03_guidelines/common/naming.md
# - docs/ai-dev-os/03_guidelines/frameworks/[stack]/overview.md
# - docs/ai-dev-os/03_guidelines/frameworks/[stack]/server-actions.md
# - docs/ai-dev-os/03_guidelines/frameworks/[stack]/api.md
# - docs/ai-dev-os/03_guidelines/frameworks/[stack]/auth.md

<!--
  All other guidelines (logging, testing, performance, cors, env,
  cicd, i18n, rate-limiting, etc.) are checked by /ai-dev-os-check
  AFTER code generation. Do NOT add them to static context.
-->

### 2. Project-Specific Guidelines
<!-- Your project's custom rules — these are always high-value because
     the AI cannot infer them from training data -->
# - docs/guidelines/project-specific/...

## Quality Workflow

<!--
  The recommended workflow (benchmark score: 96.9/100):
  1. AI generates code with the 3-5 guidelines above in context
  2. Run /ai-dev-os-check to verify against ALL guidelines
  3. AI fixes any violations found
  This "generate → check → fix" loop is more effective than
  loading all guidelines during generation.
-->

## Priority Resolution Rules (Specificity Cascade)

When rules conflict, resolve by the following priority:

1. [Highest] frameworks/[stack]/*     <- Most specific
2. [High]    common/*                 <- Common but concrete
3. [Medium]  project-specific/*       <- Project context
4. [Low]     02_decision-criteria/*   <- Abstract criteria (for human reasoning, not AI context)
5. [Lowest]  01_philosophy/*          <- Most abstract (for team alignment, not AI context)

Only when there is no explicit rule should you judge based on higher-layer principles.
