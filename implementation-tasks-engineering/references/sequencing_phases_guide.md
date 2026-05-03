# Sequencing Phases Guide

Task order must respect technical dependencies and enable incremental validation at every stage.

---

## The 11-Phase Pattern

```
┌──────────────────────────────────────────────────────────────┐
│  Phase │  Category                  │  Why first?            │
├──────────────────────────────────────────────────────────────┤
│  1     │  Setup and shared types    │  Foundation for all    │
│  2     │  Database / Data layer     │  Data foundation       │
│  3     │  Backend / API             │  Business logic        │
│  4     │  ✅ Backend Checkpoint     │  Validate before front │
│  5     │  Frontend — Base components│  UI without integration│
│  6     │  Frontend — API Client     │  Connect front to back │
│  7     │  Frontend — Integration    │  Everything together   │
│  8     │  ✅ End-to-End Checkpoint  │  Validate full flow    │
│  9     │  Property-Based Tests      │  Formal correctness    │
│  10    │  Accessibility / Polish    │  Final quality         │
│  11    │  ✅ Final Checkpoint       │  Final validation      │
└──────────────────────────────────────────────────────────────┘
```

Not all phases are always needed. Omit phases that don't apply to the current feature (e.g., no frontend → skip phases 5–8).

---

## Ordering Rules

1. **Never reference code that does not yet exist** — if task 3.1 uses `TaskRepository`, task 2.2 (which creates `TaskRepository`) must come before it
2. **Each task must be executable and testable in isolation**
3. **Checkpoints separate logical phases** and allow validation before the next phase begins
4. **Optional tests come right after the code they test** — not at the end
5. **PBT tasks come after the complete implementation** — they validate the whole system

---

## Four Sequencing Strategies

### Strategy 1: Foundation-First
Build core infrastructure before features.

```
1. Project setup and core interfaces
2. Data models and validation
3. Data access layer
4. Business logic services
5. API endpoints
6. Integration and wiring
```

**Best for:** New projects, complex systems.

---

### Strategy 2: Feature-Slice (Vertical)
Build complete features end-to-end.

```
1. Feature A: complete flow (data + backend + UI)
2. Feature B: complete flow
3. Feature C: complete flow
```

**Best for:** MVP development, early user feedback.

---

### Strategy 3: Risk-First
Tackle uncertain or complex areas first.

```
1. Most complex/uncertain components
2. External integrations
3. Core business logic
4. User interface
5. Polish and optimization
```

**Best for:** High uncertainty, proof-of-concepts, external API integrations.

---

### Strategy 4: Hybrid (Recommended for most features)
Combine approaches pragmatically.

```
1. Minimal foundation (core interfaces and types)
2. High-risk/high-value feature slice
3. Expand foundation as needed
4. Remaining feature slices
5. Integration and polish
```

---

## Handling Dependencies

**Technical dependencies** — code must exist before it can be used:
```
Task 1.1: Create database connection  ← Foundation
Task 2.1: Create User model           ← Depends on 1.1
Task 3.1: Create UserService          ← Depends on 2.1
```

**Logical dependencies** — feature must exist before another feature:
```
Task 1.1: User registration  ← Must exist first
Task 2.1: User login         ← Depends on 1.1
Task 3.1: Password reset     ← Depends on 2.1
```

**Circular dependencies** — extract an interface:
```
Task 1.1: Create IUserService and IAuthService interfaces
Task 1.2: Implement UserService using IAuthService
Task 1.3: Implement AuthService using IUserService
Task 1.4: Wire up dependency injection
```

---

## Task Scope

Each sub-task should represent **2–4 hours of focused work**.

- **Too large:** "Implement complete user management system"
- **Too small:** "Add semicolon to line 42"
- **Just right:** "Create User model with validation methods"

For AI agent execution, sub-tasks may be smaller (30–60 min of work) to enable tighter incremental validation.
