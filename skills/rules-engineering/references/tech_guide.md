# Technology Stack Guide (`tech.md`)

## Purpose

`tech.md` tells AI tools exactly what is in use so they never suggest an off-stack library, import a package you don't have, or propose a pattern that conflicts with your chosen frameworks.

## Section Breakdown

### Runtime & Language (required)

Always include version. AI tools use version to apply correct APIs.

```markdown
- Language: TypeScript 5.4 (strict mode enabled)
- Runtime: Node.js 20 LTS
```

```markdown
- Language: Python 3.12
- Runtime: CPython (no Cython, no PyPy)
```

### Frontend (include if applicable)

List framework, build tool, styling approach, and state layers separately. These are distinct decisions.

```markdown
## Frontend
- Framework: React 18 (function components only — no class components)
- Build tool: Vite 5
- Styling: TailwindCSS v3 with `clsx` for conditional classes
- Component library: shadcn/ui (copy-paste, not npm package)
- Global state: Zustand 4 (one store per feature slice)
- Server state: TanStack Query v5 (useQuery / useMutation)
- Routing: React Router v6 (file-based via src/routes/)
```

### Backend (include if applicable)

```markdown
## Backend
- Framework: Express 5
- Validation: Zod 3 (schemas in src/schemas/, shared with frontend)
- ORM: Prisma 5 (PostgreSQL adapter)
- Auth: JWT (access token 15 min / refresh token 7 days, httpOnly cookie)
- File uploads: Multer + S3 via @aws-sdk/client-s3
```

### Database & Storage (required if any persistence)

```markdown
## Database & Storage
- Primary: PostgreSQL 16 (managed via AWS RDS)
- Cache: Redis 7 (ElastiCache) — session data and rate limiting only
- Search: Elasticsearch 8 — product catalog only
- Files: AWS S3 (us-east-1 for US tenants, eu-west-1 for EU tenants)
```

### Hard Constraints (required)

Most important section. Explicit bans prevent the AI from doing the wrong thing confidently.

**Format:** `Never [do X]` / `Always [do Y] for [purpose]` / `Use [A] not [B]`.

```markdown
## Hard Constraints
- Never use `any` in TypeScript — use `unknown` + type guard or proper generic
- Never write raw SQL — all DB access through Prisma client
- Never store secrets in code — use environment variables documented in .env.example
- Never use `moment.js` — use `date-fns` (already installed)
- Always use Zod for validation at API boundaries — never trust `req.body` directly
- Use `pnpm` not `npm` or `yarn` — lockfile is pnpm-lock.yaml
- Do not add new npm dependencies without updating docs/rules/tech.md
```

## How to Handle Multiple Options

When two libraries serve the same purpose, pick one and document it. Don't list both as "options" — the AI will pick randomly.

**Bad:**
```markdown
- State: Redux or Zustand (both available)
```

**Good:**
```markdown
- Global state: Zustand — Redux is installed but unused, do not use it for new code
```

## Version Pinning vs. Range

Document what is actually installed — not aspirational versions.

```markdown
# Good — matches package.json
- React: 18.3.1

# Bad — AI may apply React 19 APIs
- React: 18+
```

## Common Anti-Patterns

| Anti-pattern | Problem | Fix |
|-------------|---------|-----|
| No versions listed | AI may use incompatible API | Add exact or major.minor |
| "Standard Node.js tooling" | Vague — many options exist | Name the specific tools |
| Missing hard constraints | AI introduces banned libraries | Add explicit `Never use X` rules |
| Backend and frontend mixed | Hard to scan | Separate into subsections |
| Listing infrastructure detail in tech.md | Wrong layer | Keep to tech choices, not operations |

## Length Target

- Each section: 3–10 bullet points
- Hard constraints: 3–10 explicit rules
- Total document: < 80 lines
