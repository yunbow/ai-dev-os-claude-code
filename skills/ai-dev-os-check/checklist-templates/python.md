# Python Checklist

## Type Hints & Static Analysis

- [ ] Functions have type hints for arguments and return values
- [ ] `Any` is not used — use `object`, `Protocol`, or specific types instead
- [ ] `Optional` / `Union` / `X | None` usage is appropriate
- [ ] `# type: ignore` is not used without a specific error code (e.g., `# type: ignore[arg-type]`)
- [ ] mypy or pyright passes in strict mode

## Validation (Pydantic)

- [ ] External input is validated via Pydantic `BaseModel`
- [ ] Pydantic models are the single source of truth for schemas
- [ ] `field_validator` / `model_validator` handles cross-field constraints
- [ ] Config files are loaded via `pydantic-settings` (not raw `os.environ`)

## Security

- [ ] SQL injection prevention — parameterized queries or ORM (SQLAlchemy)
- [ ] User input is validated at API boundaries
- [ ] Secrets are not hardcoded (use environment variables or secret managers)
- [ ] Dependencies are audited (`pip-audit` / `safety`)
- [ ] `subprocess` calls do not use `shell=True` with user input

## Error Handling

- [ ] Custom exception hierarchy is used (bare `except:` is prohibited)
- [ ] `except Exception` catches specific exceptions where possible
- [ ] Error messages include sufficient context (user ID, action, resource)
- [ ] Exceptions are raised in domain logic, caught at API/CLI boundaries
- [ ] Resources are cleaned up with context managers (`with` statement)

## API Design (FastAPI)

- [ ] Route handlers are thin — business logic is in service layer
- [ ] Request/Response models use Pydantic `BaseModel`
- [ ] Authentication is enforced via `Depends()` (not inline checks)
- [ ] Async endpoints use `async def` only when performing async I/O

## CLI Design (Typer)

- [ ] Commands use `Annotated[T, typer.Option(...)]` for options
- [ ] stdout is for data output, stderr is for diagnostics
- [ ] Exit codes follow convention: 0 (success), 1 (partial failure), 2 (fatal)

## Testing (pytest)

- [ ] Critical logic has unit tests (financial, security, data integrity)
- [ ] Tests use fixtures (`conftest.py`) for shared setup
- [ ] External API calls are mocked (`respx`, `responses`)
- [ ] Test functions follow `test_{action}_{condition}_{expected}` naming

## Code Quality

- [ ] Follows PEP 8 (enforced by Ruff)
- [ ] Functions are ≤ 30 lines; files are ≤ 300 lines
- [ ] No circular imports
- [ ] Side-effect functions are named with verbs (`fetch_`, `save_`, `send_`)
- [ ] Pure functions are named with nouns/adjectives (`calculate_`, `format_`, `is_`)

## Logging (structlog)

- [ ] Logs are structured (key-value pairs, not string interpolation)
- [ ] PII is redacted from log output
- [ ] Request trace IDs are propagated
- [ ] Logs write to stderr (stdout is reserved for program output)

## Database (SQLAlchemy)

- [ ] Sessions use context managers or dependency injection
- [ ] N+1 queries are avoided (`selectinload`, `joinedload`)
- [ ] Migrations are managed with Alembic
- [ ] Models use snake_case for table/column names
