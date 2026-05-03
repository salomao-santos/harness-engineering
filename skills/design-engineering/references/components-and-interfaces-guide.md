# Guide: Components and Interfaces

## Purpose

This section details every major system component: its responsibilities, TypeScript interfaces, dependencies, and how it communicates with other components. This is the level of detail that lets a developer start implementing without ambiguity.

## Recommended Organization

1. **Frontend — UI Components** (from root to leaf)
2. **Frontend — API Client** (backend communication functions)
3. **Backend — REST Endpoints** (endpoint table)
4. **Backend — Server Setup** (configuration and initialization)
5. **Backend — Repositories** (data access layer)
6. **Error Handling** (conventions and response format)

---

## General Rules

1. **Order from root component to leaf components**
2. **Use TypeScript for all interfaces** — no implicit `any`
3. **Optional props with `?`** — don't make required fields optional
4. **Each component has a purpose in 1–2 sentences** — no generic bullet lists
5. **Document callbacks and events** — not just data props
6. **Endpoints in a table** — method, route, request body, response, HTTP code

---

## 1. Frontend — UI Components

For each component:

```markdown
#### N. `ComponentName`

Responsibility in 1–2 sentences.

- **Props:** `prop1: Type`, `prop2?: Type`, `onAction: (param: Type) => void`
- **Internal state:** `[state, setState]` — what it tracks and why
- **Visual behaviors:** e.g., "Badge color changes by priority: red (High), yellow (Medium), gray (Low)"
- **Emits:** `onAction(param)` when the user does X
```

**Example:**

```markdown
#### 1. `Dashboard`

Root component. Fetches all tasks on mount, manages the global task list state, and
orchestrates opening and closing of modals.

- **Internal state:** `tasks: Task[]`, `selectedTask: Task | null`, `modalOpen: boolean`
- **Emits:** `handleCreate(dto)`, `handleUpdate(id, dto)`, `handleDelete(id)`

#### 2. `TaskCard`

Displays a single task inside a Kanban column.

- **Props:** `task: Task`, `onEdit: (task: Task) => void`, `onDelete: (id: number) => void`
- **Visual behaviors:** Priority badge color — `High` → red, `Medium` → yellow, `Low` → gray
```

---

## 2. Frontend — API Client

Document the backend communication layer using TypeScript function signatures.

**Rules:**
- Declare `API_BASE` explicitly
- Use typed return promises: `Promise<Type>`
- Cover all CRUD operations for the resource
- Show how HTTP errors are surfaced via `handleResponse`

`handleResponse` is a shared utility that throws on non-2xx status codes and returns the parsed JSON body:

```typescript
// src/api/handleResponse.ts
export async function handleResponse<T>(res: Response): Promise<T> {
  if (!res.ok) {
    const body = await res.json().catch(() => ({}));
    throw new Error(body.message ?? `HTTP ${res.status}`);
  }
  if (res.status === 204) return undefined as T;
  return res.json() as Promise<T>;
}
```

```typescript
// src/api/{resource}Api.ts
const API_BASE = 'http://localhost:{port}';

export const {resource}Api = {
  getAll: (): Promise<{Type}[]> =>
    fetch(`${API_BASE}/{resource}`).then(handleResponse),

  getById: (id: number): Promise<{Type}> =>
    fetch(`${API_BASE}/{resource}/${id}`).then(handleResponse),

  create: (data: Create{Type}DTO): Promise<{Type}> =>
    fetch(`${API_BASE}/{resource}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data),
    }).then(handleResponse),

  update: (id: number, data: Update{Type}DTO): Promise<{Type}> =>
    fetch(`${API_BASE}/{resource}/${id}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data),
    }).then(handleResponse),

  delete: (id: number): Promise<void> =>
    fetch(`${API_BASE}/{resource}/${id}`, { method: 'DELETE' }).then(handleResponse),
};
```

---

## 3. Backend — REST Endpoints

Present all endpoints in a table. Use `—` when there is no request body.

**Standard HTTP codes:**
- `200` — Success (GET, PUT, PATCH)
- `201` — Created (POST)
- `204` — No content (DELETE)
- `400` — Validation error
- `404` — Resource not found
- `500` — Internal server error

```markdown
| Method | Route | Description | Request Body | Response |
|--------|-------|-------------|--------------|----------|
| GET | `/api/{resource}` | List all | — | `{Type}[]` (200) |
| GET | `/api/{resource}/:id` | Get by ID | — | `{Type}` (200) |
| POST | `/api/{resource}` | Create | `Create{Type}DTO` | `{Type}` (201) |
| PUT | `/api/{resource}/:id` | Update | `Update{Type}DTO` | `{Type}` (200) |
| DELETE | `/api/{resource}/:id` | Delete | — | — (204) |
```

---

## 4. Backend — Server Setup

Show initialization with route-to-handler mapping:

```typescript
// server/index.ts
const db = new Database('app.sqlite', { create: true });
initializeDatabase(db);
seedDatabase(db);

serve({
  port: 3001,
  routes: {
    '/api/{resource}': {
      GET: () => handler.getAll(db),
      POST: (req) => handler.create(db, req),
    },
    '/api/{resource}/:id': {
      GET: (req) => handler.getById(db, req),
      PUT: (req) => handler.update(db, req),
      DELETE: (req) => handler.delete(db, req),
    },
  },
  fetch(req) {
    return new Response('Not Found', { status: 404 });
  },
});
```

---

## 5. Backend — Repositories

Document each method with its SQL query. Always use parameterized queries (`?`).

```typescript
// server/db/{resource}Repository.ts
export class {Resource}Repository {
  constructor(private db: Database) {}

  findAll(): {Type}[] {
    return this.db.query('SELECT * FROM {table} ORDER BY created_at DESC').all() as {Type}[];
  }

  findById(id: number): {Type} | null {
    return this.db.query('SELECT * FROM {table} WHERE id = ?').get(id) as {Type} | null;
  }

  create(data: Create{Type}DTO): {Type} {
    return this.db.query(
      `INSERT INTO {table} ({fields}) VALUES ({placeholders}) RETURNING *`
    ).get(...values) as {Type};
  }

  update(id: number, data: Update{Type}DTO): {Type} | null {
    return this.db.query(
      `UPDATE {table} SET {field} = ? WHERE id = ? RETURNING *`
    ).get(data.field, id) as {Type} | null;
  }

  delete(id: number): boolean {
    return this.db.run('DELETE FROM {table} WHERE id = ?', [id]).changes > 0;
  }
}
```

---

## 6. Error Handling

Document the system's error conventions in the design document:

### Error Categories

| Category | HTTP Status | Condition | User-facing Message |
|----------|-------------|-----------|---------------------|
| Validation | 400 | Missing required field or invalid format | `"{field} is required"` |
| Not Found | 404 | ID does not exist in the database | `"{Resource} not found"` |
| Internal Error | 500 | Unhandled exception | `"Internal server error"` |

### Error Response Format

```json
{
  "error": "VALIDATION_ERROR",
  "message": "Field 'title' is required"
}
```

### Backend Conventions

- Validation errors: return `400` before hitting the database
- Not found: return `404` when `findById` returns `null`
- Never expose stack traces in production responses

---

## Anti-patterns

| Anti-pattern | Problem |
|--------------|---------|
| Only documenting the happy path for endpoints | Developers don't know what to return on error |
| Props without types (`any`, `object`) | Loses all value of the type system |
| Repository methods without explicit SQL | Ambiguity about the actual implementation |
| Endpoints without HTTP status codes | Developers use incorrect codes |
| Callbacks without typed signatures | Integration bugs between components |
