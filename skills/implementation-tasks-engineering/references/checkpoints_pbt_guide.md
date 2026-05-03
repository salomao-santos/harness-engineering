# Checkpoints and Property-Based Testing Guide

---

## Checkpoints

### What Are Checkpoints?

Checkpoints are special tasks that **produce no new code**. They serve to:
1. Validate that everything built so far works (tests pass, server starts)
2. Give the user an opportunity to review and provide feedback
3. Separate logical phases of development
4. Prevent problem accumulation that only surfaces at the end

### When to Place Checkpoints

- **After the backend** (Phase 4): before starting the frontend
- **After end-to-end integration** (Phase 8): frontend + backend working together
- **At the end of the project** (Phase 11): final validation

### Checkpoint Format

```markdown
- [ ] 4. Checkpoint — Verify functional backend
  - Ensure all tests pass and the server starts correctly. Ask the user if there are any questions.
```

**Rules:**
- No sub-tasks
- Describe what must be verified
- End with "Ask the user if there are any questions"
- Use `- [ ]` (mandatory), never `- [ ]*` (optional)

---

## Property-Based Testing (PBT) Tasks

### What Are PBT Tasks?

PBT tasks validate **universal properties** of the system, not specific examples. They generate random data and verify that a property always holds.

Include PBT tasks for:
- Serialization/deserialization (round-trip)
- Data transformations (idempotency)
- Domain invariants (e.g., status is always a valid enum value)
- Operations that must preserve data (e.g., update does not alter `created_at`)

### PBT Property Patterns

```
┌──────────────────────────────────────────────────────────────┐
│  Type            │  Pattern                                  │
├──────────────────────────────────────────────────────────────┤
│  Round-trip      │  deserialize(serialize(x)) === x          │
│  Idempotency     │  f(f(x)) === f(x)                        │
│  Invariant       │  ∀x: property(x) === true                 │
│  Preservation    │  update(x, field) does not alter others   │
│  Commutativity   │  f(g(x)) === g(f(x))                     │
└──────────────────────────────────────────────────────────────┘
```

### When to Include PBT

Include a PBT task whenever the `requirements.md` contains a `FOR ALL` criterion:

```markdown
# In requirements.md:
6. FOR ALL valid Task objects, serializing to JSON and deserializing SHALL produce an equivalent object.

# Generates this PBT task:
- [ ]* 9.1 Write property-based test for Task round-trip
  - **Property: Task serialization round-trip**
  - For any valid Task object, serializing with `JSON.stringify` and deserializing with `JSON.parse`
    SHALL produce an object equivalent to the original
  - Generate random Task objects with valid values for all fields (id, title, description, priority, status, created_at)
  - Use a PBT library compatible with the runtime (e.g., `fast-check`)
  - **Validates: Requirements 12.3**
```

### PBT Task Structure

```markdown
- [ ]* 9.X Write property-based test for {Entity} {property_name}
  - **Property: {Formal property name}**
  - For any {valid object X}, {operation Y} SHALL produce {result Z}
  - Generate random data: {fields, types, valid ranges}
  - PBT library to use: {e.g., fast-check, hypothesis, QuickCheck}
  - **Validates: Requirements {N.N}**
```

### PBT Tasks Are Always Optional (`*`)

PBT tasks should always be marked with `*` (optional) because they are correctness validation — important but not required for the MVP to function. They can be added after the core implementation is complete.

---

## Examples

### Backend Checkpoint
```markdown
- [ ] 4. Checkpoint — Verify functional backend
  - Ensure all tests pass and that the server starts correctly. Ask the user if there are any questions.
```

### End-to-End Checkpoint
```markdown
- [ ] 8. Checkpoint — Verify functional end-to-end application
  - Ensure all tests pass, the backend serves the API correctly, and the frontend communicates with the backend. Ask the user if there are any questions.
```

### Final Checkpoint
```markdown
- [ ] 11. Final Checkpoint — Ensure all tests pass
  - Ensure all tests pass and the application is fully functional. Ask the user if there are any questions.
```

### PBT Round-Trip Task
```markdown
- [ ]* 9.1 Write property-based test for Task round-trip
  - **Property: Task serialization round-trip**
  - For any valid Task object, `JSON.stringify` followed by `JSON.parse` SHALL produce an equivalent object
  - Generate random Task objects with valid values: id (UUID), title (string, 1–200 chars), priority (High|Medium|Low), status (enum), created_at (ISO date)
  - Use `fast-check` library
  - **Validates: Requirements 12.3**
```

### PBT Domain Invariant Task
```markdown
- [ ]* 9.2 Write property-based test for Task status invariant
  - **Property: Task status always a valid enum value**
  - For any Task returned by the API, the `status` field SHALL be one of: "To Do", "In Progress", "Done"
  - Generate N random API calls and verify all responses
  - Use `fast-check` library
  - **Validates: Requirements 10.3**
```
