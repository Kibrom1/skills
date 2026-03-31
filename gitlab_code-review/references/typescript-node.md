# TypeScript / Node Review Heuristics
*Applies to: openclaw, detectly, and any TypeScript/Node project in this workspace*

## Type safety
- [ ] No `any` types without an explicit comment explaining why 🟡
- [ ] `unknown` preferred over `any` for inputs from external sources 🔴
- [ ] Non-null assertions (`!`) are justified — don't suppress TS errors without a good reason 🟡
- [ ] Return types are explicit on public/exported functions (aids discoverability and catches mistakes)
- [ ] Discriminated unions preferred over optional fields for modeling state variations

## Async / concurrency
- [ ] Every `Promise` is awaited or explicitly `.catch()`-ed — no floating promises 🔴
- [ ] `Promise.all` used instead of sequential awaits when operations are independent 🟡
- [ ] `for...of` with `await` is intentional (serial); use `Promise.all(arr.map(...))` for parallel 🔵
- [ ] Event emitters and streams clean up listeners to prevent memory leaks 🔴
- [ ] No `process.exit()` in library code — throw or return an error instead

## Module / architecture (openclaw-specific patterns)
- [ ] Channel providers are isolated in `src/` — no cross-channel imports that create tight coupling
- [ ] Gateway WebSocket messages are validated before processing — assume untrusted input 🔴
- [ ] Third-party extensions in `extensions/` use the defined workspace contract, not internal APIs

## Error handling
- [ ] `try/catch` blocks don't swallow errors silently — at minimum, log with context 🟡
- [ ] Errors surfaced to users are sanitized — no stack traces or internal paths exposed 🟡
- [ ] Custom error classes (or tagged error objects) are used for domain errors, not raw `Error`

## Testing (vitest)
- [ ] Mocks are typed correctly — `vi.fn<[Args], Return>()` keeps type safety
- [ ] Tests that touch async code use `await` properly; no fire-and-forget assertions
- [ ] Snapshot tests are reviewed, not just committed blindly

## Build & tooling (pnpm / tsdown / oxlint)
- [ ] `pnpm check` (format + lint + type check) passes cleanly before merge 🔴
- [ ] New dependencies added to the correct workspace (`pnpm -F <package> add`)
- [ ] `devDependencies` vs `dependencies` separation is correct — runtime deps must not be in `devDependencies`

## Common pitfalls
- Destructuring defaults that mask `undefined` in function params (can hide missing data)
- Mutating objects passed by reference from callers (unexpected side effects)
- Using `Date.now()` in tests instead of injectable time — makes tests non-deterministic
- `JSON.parse` without error handling — always wrap in try/catch 🟡
