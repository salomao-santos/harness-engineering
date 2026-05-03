# Guide: Design Decisions (ADR)

## Purpose

The Design Decisions table documents the *why* behind every significant technical choice. It prevents re-litigating settled debates, accelerates onboarding, and communicates architectural intent in a permanent, compact format — a simplified Architecture Decision Record (ADR).

## When to Fill This Section

Complete this section **before** drawing any architecture diagram. Every decision recorded here must be visibly reflected in the architecture and component interface sections that follow.

---

## Rules

1. **One row per decision** — each relevant technical area gets exactly one entry
2. **Justification is mandatory** — explain "why this and not another" in 1–2 sentences
3. **Cover all relevant categories** — use the category table below as a checklist
4. **Order by architectural impact** — runtime and database first, supporting tools last
5. **No unexplained jargon** — if you name a technology, justify why it matters for *this* project specifically

---

## Categories

Include **all** categories that apply to the project:

| Category | Examples |
|----------|----------|
| **Architectural Pattern** | Layered monolith, Microservices, Event-Driven, Hexagonal |
| Runtime / Language | Node.js, Bun, Deno, Python, Go |
| Backend Framework | Express, Fastify, Bun.serve, Hono, Django |
| Frontend Framework | React, Vue, Svelte, Angular, Solid |
| Database | PostgreSQL, SQLite, MongoDB, Redis |
| ORM / Query Builder | Prisma, Drizzle, Knex, raw SQL |
| Styling | CSS Modules, Tailwind, Styled Components, SCSS |
| State Management | Redux, Zustand, Context API, React hooks |
| Client-Server Communication | REST, GraphQL, gRPC, WebSocket, tRPC |
| Build / Bundler | Vite, Webpack, esbuild, Turbopack |
| Testing | Jest, Vitest, Playwright, Cypress |
| Authentication | JWT, Session, OAuth 2.0, Passkeys |
| Deploy / Infrastructure | Docker, AWS, Vercel, Railway, Kubernetes |
| Type System | TypeScript strict, JSDoc, untyped |

**Architectural Pattern must always be the first decision row.** It constrains every other choice — runtime, database, and communication style all follow from it.

### Choosing an Architectural Pattern

| Pattern | Choose when | Avoid when |
|---------|------------|------------|
| **Layered Monolith** | Single team, moderate scale, simple deployment, early-stage product | Independent scaling per feature is needed, teams need autonomy per service |
| **Microservices** | Teams own services independently, features scale at different rates, fault isolation is critical | Small team, no clear service boundaries, no container orchestration in place |
| **Event-Driven** | Services must be decoupled, async processing is acceptable, auditability matters | Low latency is required, team unfamiliar with event brokers, debugging complexity is a concern |
| **Hexagonal (Ports & Adapters)** | Core domain logic must be isolated from frameworks and DB, high testability is required | Simple CRUD apps, small teams, unnecessary abstraction overhead |

---

## Format

```markdown
| Decision | Choice | Rationale |
|----------|--------|-----------|
| {Area} | {Technology/Approach} | {Why this and not another — 1-2 sentences} |
```

---

## Worked Example

```markdown
| Decision | Choice | Rationale |
|----------|--------|-----------|
| Runtime | Bun | Single runtime for server, DB, and package management. Native TypeScript and SQLite support. |
| HTTP Server | Bun.serve() with routes | High-performance native router. No external framework needed. |
| Database | bun:sqlite | Embedded SQLite, synchronous API, 3–6x faster than better-sqlite3. Zero infrastructure dependencies. |
| Frontend | React 19 + Vite 8 | Mature SPA ecosystem. Vite provides fast HMR with Bun. |
| Styling | CSS Modules | Component-scoped styles. No CSS-in-JS runtime overhead. |
| State | React hooks (useState/useEffect) | Sufficient for app complexity. No external state library needed. |
| Communication | REST + JSON via fetch | Simple, well-supported, suitable for CRUD operations. |
| Type System | TypeScript strict | Compile-time type safety. Types shared across frontend and backend layers. |
```

---

## Anti-patterns

| Anti-pattern | Problem | Fix |
|--------------|---------|-----|
| "We use React because it's popular" | Doesn't justify for *this* project | "React because the team has existing expertise and the component ecosystem reduces development time" |
| Empty rationale: "best option" | Doesn't document the reasoning | Name the deciding criterion: performance, familiarity, cost, or integration needs |
| Skipping relevant categories | Implicit decisions cause implementation surprises | Use the category table as a checklist before moving on |
| Contradictory decisions | e.g., choosing GraphQL but claiming "simplicity" | Review coherence across all decisions before proceeding |

---

## Non-Functional Requirements in Design Decisions

Beyond technology choices, the ADR table must reflect decisions that address non-functional quality attributes. Reference ISO/IEC 25010 where relevant:

| Quality Attribute | Design Decision Area |
|------------------|---------------------|
| **Performance Efficiency** | Database indexing strategy, caching layer, async processing |
| **Reliability** | Circuit breaker pattern, retry/backoff, dead letter queues |
| **Security** | Auth mechanism, input validation strategy, secret management |
| **Maintainability** | Modularity (monolith vs services), code style enforcement, test strategy |
| **Portability** | Containerization (Docker), environment configuration (Twelve-Factor: config in env) |

If the system has explicit performance targets (e.g., "< 200ms p95", "100k events/second"), add a row for it in the ADR table.

---

## Checklist Before Moving to Architecture

- [ ] **Architectural Pattern** is documented and is the first row
- [ ] Runtime/language is documented
- [ ] Backend framework is documented (if applicable)
- [ ] Frontend framework is documented (if applicable)
- [ ] Database is documented
- [ ] Client-server communication strategy is documented
- [ ] Type system approach is documented
- [ ] Relevant non-functional attributes (performance, security, reliability) are addressed
- [ ] All rationales have 1–2 sentences explaining "why this and not another"
- [ ] Decisions are coherent with each other (no contradictions)
