# Python / FastAPI Backend Review Heuristics
*Applies to: brightsteps, mcp_hub, BridgeMind, and any FastAPI project in this workspace*

## FastAPI-specific
- [ ] Route handlers are thin — business logic lives in service layer, not in the route function 🟡
- [ ] Pydantic v2 models used for request/response validation — no raw `dict` inputs from `Request` 🔴
- [ ] `response_model` is set on routes that return data — prevents accidental data leakage 🟡
- [ ] Dependency injection (`Depends`) used for DB sessions and auth — not global state 🔴
- [ ] Background tasks (`BackgroundTasks`) don't share mutable state with the request lifecycle

## SQLAlchemy / database
- [ ] DB sessions are opened per-request via `Depends` and closed properly — no session leaks 🔴
- [ ] Queries filter/paginate before returning — no unbounded `.all()` on large tables 🔴
- [ ] Relationships use appropriate `lazy=` strategy — watch for N+1 in nested serialization 🔴
- [ ] Alembic migrations exist for schema changes — schema changes are not applied ad hoc 🔴
- [ ] Transactions wrap multi-step operations that must be atomic 🔴

## Auth (JWT)
- [ ] JWT tokens are validated on every protected route — `get_current_user` dependency used 🔴
- [ ] Token expiry is checked; refresh token logic handles edge cases (expired, revoked)
- [ ] Passwords are hashed with bcrypt/argon2 — never stored or logged in plain text 🔴

## Async correctness
- [ ] CPU-bound work is offloaded to `run_in_executor` or a worker — not blocking the event loop 🔴
- [ ] Async SQLAlchemy (`AsyncSession`) used consistently if the app is async — no mixing of sync/async DB calls
- [ ] HTTP calls use `httpx.AsyncClient`, not `requests` (which blocks the event loop) 🟡

## Pydantic v2 patterns
- [ ] `model_validator` preferred over ad-hoc validation in route handlers
- [ ] `Field(...)` with validators for constrained inputs (email, positive int, etc.)
- [ ] `model_config = ConfigDict(from_attributes=True)` set on ORM-mapped models

## Error handling
- [ ] `HTTPException` used for client errors (4xx); unexpected exceptions handled by a global exception handler
- [ ] No raw tracebacks or internal paths leaked in 5xx responses 🟡
- [ ] Custom exception handlers registered for domain errors

## Testing (pytest)
- [ ] `TestClient` or `AsyncClient` used for route tests — avoids spinning up a real server
- [ ] DB is isolated per test using transactions that roll back, or a separate test DB 🟡
- [ ] Fixtures use `scope="function"` by default; broader scopes only when justified
- [ ] `pytest.mark.asyncio` or `anyio` configured for async tests

## Common pitfalls
- Mutable default arguments in function signatures (`def foo(items=[])`) — always use `None` 🔴
- `from __future__ import annotations` breaking Pydantic v2 model resolution at runtime
- Returning ORM objects directly from routes without a `response_model` — serializes unexpected fields
- Missing `await` on async DB calls — silently returns a coroutine instead of the result 🔴
