---
name: implementation-tasks-engineering
description: >
  Transform approved requirements and design documents into a sequenced, incremental
  implementation plan (tasks.md). Use when: design phase is complete and approved,
  user asks to create a task plan, break down the implementation, generate coding tasks,
  or write an implementation plan. Trigger phrases: "create tasks", "generate implementation plan",
  "break down the design into tasks", "write the tasks doc", "implementation tasks",
  "task breakdown", "create tasks.md".
  Also use this skill whenever someone has a design or requirements doc ready and
  wants to know what to code next — even if they don't say "tasks" or "implementation plan".
license: MIT
compatibility: Claude Code, Cursor, VS Code, Windsurf, Kiro, Github Copilot, Antigravity
metadata:
  category: methodology
  complexity: intermediate
  author: Jenny Santos
  version: "1.0.0"
---

# Implementation Tasks Engineering

Transform approved `requirements.md` and `design.md` into a structured, incremental `tasks.md` that serves as a direct implementation roadmap. Tasks are never invented in a vacuum — every task is derived from the design and traceable to a requirement.

## When to Use This Skill

- Requirements and design phases are complete and approved
- Ready to begin the implementation phase
- Need to coordinate work across developers or AI agents
- Want to track incremental progress during coding
- Planning sprints or work assignments for a feature

---

## 7-Step Workflow

### Step 1: Analyze Inputs

Read `requirements.md` and `design.md` in full before generating any task. Extract two structured maps:

**From `requirements.md`:** user stories, acceptance criteria, EARS constructions (SHALL/WHEN/IF/WHILE/FOR ALL), correctness properties, and the Glossary.

**From `design.md`:** architecture decisions, component list, TypeScript types/interfaces, SQL schema, API endpoints, UI components with props, data flow, directory structure, and seed data.

→ See [task_generation_process_guide.md](references/task_generation_process_guide.md) for the full extraction table and mapping rules.

### Step 2: Write the Overview

Open `tasks.md` with a 2–4 sentence overview covering: what the plan implements, the technology stack (runtime, framework, database), the incremental philosophy (no orphan code), and the total number of main tasks and phases.

```markdown
This plan converts the design of {feature} into incremental coding tasks.
Stack: {stack}. Each task builds on the previous ones — no orphan code.
The plan contains {N} main tasks organized in {N} phases, derived from
requirements.md ({N} requirements) and design.md.
```

### Step 3: Map Design to Implementation Units

For every item in `design.md`, identify the corresponding task. Each design artifact must have at least one task:

| Design artifact | Generates |
|----------------|-----------|
| TypeScript types | Setup task (types file) |
| SQL table | Data layer task (schema + repository) |
| API endpoint | Backend task (route handler) |
| UI component | Frontend task (`.tsx` + `.module.css`) |
| Serialization/deserialization | PBT task |
| Seed data | Seed task |

→ See [task_generation_process_guide.md](references/task_generation_process_guide.md) for the complete mapping table.

### Step 4: Sequence Tasks by Phase

Order tasks to respect dependencies and enable incremental validation. Follow the 11-phase pattern: Setup → Data → Backend → ✅ Checkpoint → Frontend Components → API Client → Integration → ✅ Checkpoint → PBT → Accessibility → ✅ Final Checkpoint.

Rules:
1. Never reference code that does not yet exist in a previous task
2. Each task must be executable and testable in isolation
3. Checkpoints separate logical phases and force validation gates

→ See [sequencing_phases_guide.md](references/sequencing_phases_guide.md) for the full phase table, ordering rules, and sequencing strategies (Foundation-First, Feature-Slice, Risk-First, Hybrid).

### Step 5: Write Task Details

For each sub-task, include enough detail for a developer or agent to implement without ambiguity:

- **File path**: use full path (e.g., `server/db/taskRepository.ts`)
- **What to implement**: class, function, method names and signatures
- **Concrete values**: hex colors, HTTP codes, SQL constraints, enum values, UI text
- **Component names**: use the exact names from `design.md` (Props, interface names, route paths)
- **Traceability**: end every sub-task with `_Requirements: N.N_` in italic

→ See [task_syntax_guide.md](references/task_syntax_guide.md) for the checkbox syntax, status markers, optional tasks (`*`), and the full sub-task anatomy.

### Step 6: Add Checkpoints and PBT Tasks

**Checkpoints** are special tasks that produce no new code. They validate that everything built so far works before moving to the next phase. Place them after: the backend phase, the end-to-end integration, and the final implementation. Format: a single bullet, no sub-tasks, ending with "Ask the user if there are any questions."

**PBT (Property-Based Testing) tasks** validate universal correctness properties — not specific examples. Include them for: round-trip serialization, idempotency of operations, domain invariants, and field-preservation in updates.

→ See [checkpoints_pbt_guide.md](references/checkpoints_pbt_guide.md) for checkpoint format rules, PBT property patterns, and worked examples.

### Step 7: Validate and Deliver

Before presenting `tasks.md`, run the quality checklist:

- Every component in `design.md` has at least one task
- Every task references at least one requirement from `requirements.md`
- No task references code that does not yet exist in earlier tasks
- Each sub-task has enough detail for implementation without ambiguity
- File paths are named with full paths
- Concrete values are present (colors, texts, HTTP codes, method names)
- Checkpoints are placed between logical phases
- Optional tasks are marked with `*`
- PBT tasks cover all correctness properties from the requirements
- Numbering is sequential with no gaps (1, 1.1, 1.2, 2, 2.1 …)

→ See [quality_checklist_guide.md](references/quality_checklist_guide.md) for the full checklist, anti-patterns to avoid, task categories, risk management, and the handoff to the implementation phase.

---

## Checklist

Before delivering, run the checklist in [quality_checklist_guide.md](references/quality_checklist_guide.md).

### Quick Reference
- [ ] Every component in `design.md` has at least one task
- [ ] Every sub-task ends with `_Requirements: N.N_`
- [ ] No task references code not yet created in a prior task
- [ ] Full file paths and concrete values throughout (HTTP codes, method names, SQL constraints)
- [ ] Checkpoints between logical phases; PBT tasks for correctness properties
- [ ] Optional tasks marked with `*`; numbering sequential and gap-free
- [ ] Development team has reviewed and approved

---

## Quick Example

```markdown
## Overview

This plan converts the design of the Task System into incremental coding tasks.
Stack: Bun + TypeScript (backend), React + Vite + CSS Modules (frontend), SQLite via bun:sqlite.
Each task builds on the previous ones — no orphan code. The plan contains 11 main tasks
organized in 11 phases, derived from requirements.md (12 requirements) and design.md.

## Tasks

### Phase 1 — Setup and Shared Types

- [ ] 1. Project setup and shared types
  - [ ] 1.1 Initialize project with Bun and configure directory structure
    - Create `package.json` with scripts: `bun run server` and `bun run dev`
    - Initialize Vite with React + TypeScript template in `src/`
    - Create `server/` directory for the backend
    - Install dependencies: `react`, `react-dom`, `@types/react`, `@types/react-dom`
    - _Requirements: 10.1_

  - [ ] 1.2 Define shared TypeScript types
    - Create `server/types.ts` with: `TaskStatus`, `Task`, `CreateTaskDTO`, `UpdateTaskDTO`, `ApiError`
    - Create `src/types.ts` in the frontend mirroring the backend types
    - _Requirements: 12.1, 12.2_

### Phase 2 — Data Layer

- [ ] 2. SQLite database
  - [ ] 2.1 Create schema and database initialization
    - Create `server/db/init.ts` with `initializeDatabase(db)` function
    - Create tables `tasks` and `comments` with `CREATE TABLE IF NOT EXISTS`
    - Enable `PRAGMA foreign_keys = ON` and `PRAGMA journal_mode = WAL`
    - Include CHECK constraints for `priority` (High, Medium, Low) and `status` (To Do, In Progress, Done)
    - _Requirements: 10.2, 10.3_

  - [ ] 2.2 Implement TaskRepository
    - Create `server/db/taskRepository.ts` with class `TaskRepository`
    - Methods: `findAll()`, `findById(id)`, `create(data)`, `update(id, data)`, `delete(id)`
    - Use parameterized queries; use `RETURNING *` after INSERT/UPDATE
    - _Requirements: 9.1, 10.1_

  - [ ]* 2.3 Write unit tests for TaskRepository
    - Test full CRUD; test CASCADE on delete; test ordering by `created_at` DESC
    - _Requirements: 10.4, 10.5_

### Phase 3 — Backend / API

- [ ] 3. REST API with Bun.serve
  - [ ] 3.1 Implement task routes
    - Create `server/routes/tasks.ts` with handlers:
      - `GET /api/tasks` — list all tasks
      - `POST /api/tasks` — create task (default status "To Do", returns 201)
      - `PUT /api/tasks/:id` — update task
      - `DELETE /api/tasks/:id` — delete task (returns 204)
    - Return `Response.json()` with correct HTTP status (200, 201, 204, 400, 404)
    - _Requirements: 9.1, 9.4_

### Phase 4 — Backend Checkpoint

- [ ] 4. Checkpoint — Verify functional backend
  - Ensure all tests pass and the server starts correctly. Ask the user if there are any questions.
```

---

## Output

Use the blank template at [assets/tasks-template.md](assets/tasks-template.md) as the starting point for a new `tasks.md` document.
