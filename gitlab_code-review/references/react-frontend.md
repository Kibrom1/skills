# React Frontend Review Heuristics
*Applies to: brightsteps/frontend, mcp_hub/mcp-hub-ui, BridgeMind/ui, detectly, and any React project*

## Component design
- [ ] Components have a single, clear purpose — "smart" data-fetching and "dumb" display components are separated 🟡
- [ ] Props interfaces are typed explicitly — no `props: any` or missing prop types 🟡
- [ ] State is as local as possible — lifted only when genuinely shared between siblings
- [ ] Derived state is computed from existing state/props, not duplicated in `useState` (source of truth bugs) 🟡

## Hooks
- [ ] `useEffect` dependencies array is complete and correct — missing deps can cause stale closures 🔴
- [ ] Effects that set up subscriptions or intervals have a cleanup return function 🔴
- [ ] `useMemo` / `useCallback` are used judiciously — only when profiling shows a real win, not by default 🔵
- [ ] Custom hooks follow the `use` naming convention and encapsulate a clear concern

## React Query (TanStack Query) patterns
- [ ] Query keys are stable and deterministic — include all variables the query depends on 🔴
- [ ] `isLoading`, `isError` states are handled in the UI — no silent failures 🟡
- [ ] Mutations call `queryClient.invalidateQueries` after success to keep the cache fresh 🟡
- [ ] `staleTime` and `cacheTime` are tuned to the data's actual freshness requirements

## Performance
- [ ] Lists render a stable `key` prop — not array index unless the list is static 🔴
- [ ] Large lists use virtualization (react-window / tanstack-virtual) for 100+ items 🟡
- [ ] Heavy components are `React.memo`-ized only when re-render profiling justifies it 🔵
- [ ] Images have explicit `width`/`height` to prevent layout shift

## Security
- [ ] `dangerouslySetInnerHTML` is never used with user-controlled content 🔴
- [ ] API responses are not rendered directly as HTML
- [ ] Sensitive tokens are not stored in `localStorage` (prefer memory or httpOnly cookies) 🔴
- [ ] User input is trimmed and validated client-side (as a UX aid, not a security gate — server validates too)

## Routing (React Router v6)
- [ ] Protected routes redirect to login when the user is unauthenticated
- [ ] Route params are validated before use — no assumption they're valid strings
- [ ] `useNavigate` used for programmatic navigation, not `window.location`

## Styling (Tailwind / MUI)
- [ ] Utility classes are not duplicated across many components — extract a shared component or `@apply` rule
- [ ] Responsive breakpoints (`sm:`, `md:`, etc.) are tested — not just desktop
- [ ] MUI `sx` prop usage is consistent with the team's styling approach

## Testing (Vitest / Playwright)
- [ ] Components are tested via `@testing-library/react` — test behavior, not implementation
- [ ] Queries use accessible selectors (`getByRole`, `getByLabelText`) rather than test IDs where possible
- [ ] Async interactions use `waitFor` or `findBy*` — not `setTimeout` hacks 🟡

## Common pitfalls
- Closing over stale state in event handlers inside `useEffect` — use `useRef` or include in deps
- Forgetting to handle the unmounted component case in async effects (React 18 strict mode doubles mount)
- Prop drilling more than 2–3 levels — consider context or a state library
- API base URL hardcoded instead of from `VITE_*` env variable
