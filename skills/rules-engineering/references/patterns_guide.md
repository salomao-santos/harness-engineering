# Project Patterns Guide (`patterns.md`)

## Purpose

`patterns.md` captures the **how** — recurring implementation decisions the AI must follow or avoid. It answers: "given this codebase, what is the right way to write X?" Without it, the AI uses generic patterns that may conflict with your established conventions.

## Section Breakdown

### Architectural Style (required)

One focused paragraph. Name the style, then describe its key rule.

```markdown
**Feature-Sliced Design.** Each feature (`src/features/`) is a self-contained
module: its own components, hooks, API calls, and local state. Features communicate
only through shared state (Zustand) or URL params — never by importing each other.
New work always starts by identifying the feature it belongs to.
```

```markdown
**Hexagonal architecture (Ports & Adapters).** Business logic lives in
`src/domain/` and has zero external dependencies. Adapters in `src/adapters/`
implement ports and inject real implementations at runtime. Tests use fake adapters.
```

### Data Fetching (required)

Table + one short example for the primary case.

| Scenario | Pattern |
|----------|---------|
| Read server data (list, detail) | TanStack Query `useQuery` with typed query key factory |
| Create / update / delete | TanStack Query `useMutation` + `queryClient.invalidateQueries` |
| Optimistic update | `useMutation` `onMutate` + `onError` rollback |
| Realtime push | Custom `useSocket(channel)` hook — wraps WebSocket, returns typed events |
| Global client state | Zustand slice — one file per feature in `src/features/*/store.ts` |

```ts
// Query key factory pattern — all keys centralized
export const taskKeys = {
  all: ['tasks'] as const,
  list: (projectId: string) => [...taskKeys.all, 'list', projectId] as const,
  detail: (id: string) => [...taskKeys.all, 'detail', id] as const,
}

export function useTaskList(projectId: string) {
  return useQuery({
    queryKey: taskKeys.list(projectId),
    queryFn: () => fetchTasks(projectId),
  })
}
```

### Error Handling (required)

```markdown
**API errors** are normalized to `ApiError { code: string; message: string; details?: string[] }`
by the Axios response interceptor in `src/shared/api/client.ts`.

**UI errors** surface at three levels:
1. Field-level: Zod parse errors returned from `useMutation` → shown inline below input
2. Toast: non-blocking errors (network retry failure) → `useToast()` from `src/shared/hooks/`
3. Page-level: React Error Boundary per route — catches render crashes, shows fallback UI

**Logging:**
- Development: `console.error` with full stack
- Production: Sentry `captureException` via `src/shared/monitoring.ts`

**Rule:** Never swallow errors silently. Every `catch` must either re-throw, log, or surface to the user.
```

### State Management (required)

```markdown
**Decision table:**

| Data type | Where to store |
|-----------|---------------|
| UI-only (modal open, active tab) | `useState` / `useReducer` in component |
| Async server data | TanStack Query cache |
| Cross-feature shared client state | Zustand store in relevant feature |
| Auth session (user, token) | `src/features/auth/store.ts` (Zustand) |
| URL-driven state (filters, pagination) | URL search params via `useSearchParams` |

**Rule:** Do not put server data in Zustand. Do not put UI-only state in TanStack Query.
```

### Validation (required)

```markdown
**Schemas** live in `src/schemas/` and are shared between frontend and backend via a
workspace package (`packages/schemas`).

**Validate at two points:**
1. API boundary — `req.body` parsed with Zod before reaching any service
2. Form submission — same Zod schema via `react-hook-form` resolver

**Rule:** Never use `as SomeType` to cast unvalidated external data. Parse with Zod;
on failure, return a 400 with the Zod error message.
```

### Authentication & Authorization (required if any auth)

```markdown
**Auth flow:**
- Login → POST /auth/login → server sets httpOnly cookie (refresh token) + returns access token
- Access token stored in memory (Zustand auth store) — never localStorage
- Axios interceptor attaches `Authorization: Bearer <token>` to every request
- On 401 → interceptor calls POST /auth/refresh silently; retries original request once
- On refresh failure → logout, redirect to /login

**Authorization:**
- Permission check: `can(user, action, resource)` helper (`src/features/auth/permissions.ts`)
- Route protection: `<RequireAuth requiredRole="member" />` wrapper
- Never trust client-side permission checks for sensitive mutations — always re-check on server
```

### Testing Strategy (required)

```markdown
| Layer | Tool | What to test | What NOT to test |
|-------|------|-------------|-----------------|
| Unit | Vitest | Pure functions, Zod schemas, Zustand actions | UI rendering |
| Component | Vitest + Testing Library | User interactions, rendered output | Implementation details |
| Integration | Vitest + MSW | Full feature flow (form → API → state update) | Third-party library internals |
| E2E | Playwright | Critical paths only: login, task CRUD, billing | Every edge case |

**Mocking policy:** Use MSW for API mocks — never `vi.mock` on fetch/axios.
**Test file location:** Co-located next to source as `*.test.ts(x)`.
**Naming:** Describe behavior, not implementation: `"shows error when title is empty"` not `"calls setError"`.
```

### Anti-Patterns (required)

Use a table. The "Use instead" column is as important as the ban.

| Anti-pattern | Why banned | Use instead |
|-------------|-----------|------------|
| `any` in TypeScript | Breaks type safety silently | `unknown` + Zod parse or type guard |
| Raw SQL strings | SQL injection risk; breaks migration history | Prisma client always |
| Direct `document.querySelector` | Bypasses React reconciler | `useRef` + React state |
| Prop drilling > 2 levels | Tight coupling, hard to refactor | Context or Zustand |
| `useEffect` for data fetching | Race conditions, no caching | TanStack Query |
| `moment.js` imports | Large bundle, deprecated | `date-fns` (already installed) |
| Class components | Legacy API, no hooks support | Function components |
| `console.log` in committed code | Log noise in production | `logger.debug` from `src/shared/logger.ts` |
| Hard-coded base URLs | Breaks across environments | `VITE_API_URL` env var |

## Writing Good Pattern Entries

**Show, don't just tell.** For non-obvious patterns, add a short code example.

**Bad:**
```markdown
- Use proper React patterns for forms
```

**Good:**
```markdown
- All forms use `react-hook-form` + Zod resolver. Schema is the single source of
  validation truth — no manual `if (!value)` checks inside handlers.
```

**Include the why for every ban.** "Never X" without a reason gets ignored or forgotten.

**Bad:**
```markdown
- Don't use class components
```

**Good:**
```markdown
- Never use class components — no hooks support, error boundaries excepted
```

## Length Target

- Each section: 5–20 lines (add code snippet only when the pattern isn't obvious)
- Anti-patterns table: 6–12 rows
- Total document: < 150 lines
