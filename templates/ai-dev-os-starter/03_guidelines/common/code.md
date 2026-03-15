# Coding Guidelines

## Basic Rules

### Type Safety

#### Python
- MUST: Prohibit use of `Any`. Use `object`, `Protocol`, or specific types instead
- MUST: Add type hints to all function arguments and return values
- MUST: Run `mypy --strict` or `pyright` with no errors

#### TypeScript
- MUST: Prohibit use of `any` type. Use `unknown` and narrow with type guards
- MUST: Explicitly type function arguments and return values

### Naming
- MUST: Use names that express intent for variables and functions
- MUST NOT: Use abbreviations (`usr` → `user`, `btn` → `button`)

### Error Handling

#### Python
- MUST: Never swallow errors (bare `except:` is prohibited)
- MUST: Use custom exception classes with context information
- MUST: Clean up resources with context managers (`with` statement)

#### TypeScript
- MUST: Never swallow errors (empty catch blocks are prohibited)
- MUST: Include context information in error messages

### Security
- MUST NOT: Hardcode secrets (API keys, passwords, etc.) in code
- MUST: Always validate user input at system boundaries

### Code Quality
- MUST: Keep functions ≤ 30 lines, files ≤ 300 lines
- MUST: Name side-effect functions with verbs (`fetch_`, `save_`, `send_`)
- MUST: Name pure functions with nouns/adjectives (`calculate_`, `format_`, `is_`)
