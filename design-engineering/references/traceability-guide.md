# Guide: Traceability and Design Validation

## Purpose

Traceability ensures that every approved requirement has a corresponding design element, and that no design component exists without a justifying requirement. It is the final bridge between `requirements.md` and `design.md`, and the entry point for generating implementation tasks.

---

## Traceability Matrix

Map each requirement to the design components that implement it.

**Format:**

```markdown
| Requirement | Design Components |
|-------------|------------------|
| Req {N}: {Title} | `{UIComponent}`, {HTTP Method} `{route}`, `{Repository.method()}`, schema `{table}` |
```

**Rules:**
1. **Every requirement row must have at least one design component**
2. **Include at minimum:** the UI component, the endpoint, and the repository/schema involved
3. **Use exact names** from the Components and Interfaces section
4. **No requirement left uncovered** — a blank component column means the design is incomplete

**Worked example:**

```markdown
| Requirement | Design Components |
|-------------|------------------|
| Req 1: Display tasks by status | `Dashboard`, `KanbanColumn`, `TaskCard`, GET `/api/tasks`, `TaskRepository.findAll()`, schema `tasks` |
| Req 2: Create a new task | `TaskFormModal`, POST `/api/tasks`, `TaskRepository.create()`, schema `tasks` |
| Req 3: Edit an existing task | `TaskFormModal` (edit mode), PUT `/api/tasks/:id`, `TaskRepository.update()` |
| Req 4: Delete a task | `ConfirmDialog`, DELETE `/api/tasks/:id`, `TaskRepository.delete()` |
| Req 5: Comment on a task | `TaskDetailsModal`, `CommentList`, POST `/api/tasks/:id/comments`, `CommentRepository.create()` |
| Req 6: JSON round-trip serialization | DTOs, `handleResponse()`, serialization mapping, `PRAGMA foreign_keys` |
```

---

## Coverage Checklist

Run this checklist before delivering the design for review.

### Completeness

- [ ] Every approved requirement has at least one corresponding design element
- [ ] Every Glossary term from the requirements has a TypeScript type or component counterpart
- [ ] Every endpoint in the table has success **and** error HTTP codes documented
- [ ] Every required field has `NOT NULL` in the SQL schema
- [ ] Every enum field has a `CHECK` constraint in SQL and a union type in TypeScript

### Consistency

- [ ] Entity names are identical in the ER diagram, SQL schema, and TypeScript (or explicitly mapped)
- [ ] CreateDTO fields are a strict subset of the entity (no `id` or `created_at` at creation time)
- [ ] Every `REFERENCES` has an `ON DELETE` clause defined
- [ ] The JSON serialization mapping covers all entity fields

### Feasibility

- [ ] Design decisions (ADR) are coherent with each other (no contradictions)
- [ ] The directory structure reflects the chosen technology decisions
- [ ] Every documented component has declared and achievable dependencies

### Bidirectional Traceability

- [ ] Every requirement → has a design element (requirement coverage)
- [ ] Every design component → has a requirement (no orphaned components)
- [ ] Every ADR decision → is visible in the architecture diagram

---

## Validation Dimensions

| Dimension | Validation Question |
|-----------|---------------------|
| **Completeness** | Are all requirements covered? |
| **Consistency** | Are all design elements coherent with each other? |
| **Clarity** | Can a developer implement from this design without ambiguity? |
| **Feasibility** | Is the design technically achievable with the chosen technologies? |

---

## Common Problems and Fixes

| Problem | Symptom | Fix |
|---------|---------|-----|
| Requirement without design | Empty "Design Components" column | Add the missing component, endpoint, or type |
| Design component without requirement | Component doesn't appear in the matrix | Verify it's truly needed; if so, add a supporting requirement |
| TypeScript type without a DB entity | Interface exists but no SQL table | Decide: is this a persisted entity or just a transfer DTO? |
| Endpoint without 404 documented | Table only shows success response | Add error rows to the Error Handling section |
| Enum union type without SQL CHECK | Invalid values can enter the database silently | Add `CHECK(field IN (...))` to the schema |

---

## Handoff to Implementation

When the design is approved:

1. **The design is the contract** — any deviation during implementation requires updating the design document first
2. **Tasks (`tasks.md`)** derive directly from the components listed in the traceability matrix — one task per component group
3. **Tests** derive from the acceptance criteria in requirements + the components mapped in the design
4. **When a requirement changes**, use the matrix to identify which design components are impacted before touching code
