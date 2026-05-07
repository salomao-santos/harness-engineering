# Project Patterns

<!-- File: docs/rules/patterns.md -->
<!-- Purpose: Captures recurring implementation decisions AI tools must follow or avoid. -->
<!-- Be specific: show code snippets for non-obvious patterns. -->

## Architectural Style

<!-- Describe the overall architecture. One paragraph max. -->

[e.g., Feature-sliced design. Each feature is a self-contained module with its own
components, hooks, and API calls. Cross-feature communication via shared state only.
No direct imports between feature modules.]

## Data Fetching

| Scenario | Approach |
|----------|---------|
| Server data (lists, details) | [e.g., React Query with typed query keys] |
| Mutations (create/update/delete) | [e.g., React Query `useMutation` + optimistic update] |
| Global app state | [e.g., Zustand slice per feature] |
| Realtime / push | [e.g., WebSocket via custom `useSocket` hook] |

<!-- Add a short example for the primary pattern: -->
```ts
// [Primary data-fetching pattern example]
```

## Error Handling

<!-- Describe how errors are caught, surfaced, and logged. -->

- API errors: [e.g., all errors normalized to `ApiError` type with `code` + `message`]
- UI errors: [e.g., React Error Boundaries per route; inline field errors from Zod]
- Logging: [e.g., `console.error` in dev; send to Sentry in prod]
- Never swallow errors silently — always log or surface to user

```ts
// [Error handling pattern example]
```

## State Management

<!-- Describe when to use local vs. global state. -->

- Component state: `useState` / `useReducer` — for UI-only state (open/closed, active tab)
- Server state: React Query — for anything fetched from the API
- Global client state: [e.g., Zustand] — for auth session, user preferences, cart
- Do not put server data in Zustand; do not put UI state in React Query

## Authentication & Authorization

<!-- Describe how auth works in code. -->

- Auth tokens stored in: [e.g., httpOnly cookie / memory / localStorage — and why]
- Token refresh: [e.g., silent refresh via interceptor on 401]
- Route protection: [e.g., `<ProtectedRoute>` wrapper checks `useAuth()` context]
- Permission checks: [e.g., `can(user, 'edit', resource)` helper from `src/auth/`]

## Validation

- Input validation: [e.g., Zod schemas defined in `src/schemas/` and shared FE+BE]
- Validate at: [e.g., API boundary (request body) + form submission (client)]
- Never trust client-side validation alone for security-sensitive operations

## Testing Strategy

| Layer | Tool | Scope |
|-------|------|-------|
| Unit | [e.g., Vitest] | Pure functions, utilities, hooks |
| Component | [e.g., Testing Library] | Component behavior, not implementation |
| Integration | [e.g., Vitest + msw] | Feature flows with mocked API |
| E2E | [e.g., Playwright] | Critical paths only (auth, checkout, …) |

- Mock policy: [e.g., use MSW for API mocks — never mock modules directly]
- Test file location: [e.g., co-located, `*.test.ts`]

## Security Patterns

- Never commit secrets — use `.env` (gitignored) and document in `.env.example`
- Sanitize all user-generated content before rendering
- [e.g., Use parameterized queries — never concatenate SQL strings]
- [e.g., Validate file uploads: type, size, extension on server side]

## Anti-Patterns (Banned)

<!-- Explicit list of things NOT to do. AI tools will avoid these. -->

| Anti-pattern | Why banned | Use instead |
|-------------|-----------|------------|
| `any` in TypeScript | Loses type safety | `unknown` + type guard |
| Raw SQL schema changes | Breaks migration history | Prisma migrations |
| Prop drilling >2 levels | Tight coupling | Context / Zustand |
| Direct DOM manipulation | Bypasses React | refs + state |
| `console.log` in production | Log noise | Structured logger |
| [Other] | [Reason] | [Alternative] |
