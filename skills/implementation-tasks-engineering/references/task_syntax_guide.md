# Task Syntax Guide

---

## Checkbox Status Markers

```
┌──────────────────────────────────────────────────────────────┐
│  Syntax     │  Status         │  Meaning                     │
├──────────────────────────────────────────────────────────────┤
│  - [ ]      │  Not started    │  Pending task                │
│  - [x]      │  Done           │  Completed task              │
│  - [-]      │  In progress    │  Task being executed         │
│  - [~]      │  Queued         │  Task queued for execution   │
└──────────────────────────────────────────────────────────────┘
```

---

## Mandatory vs Optional Tasks

```
┌──────────────────────────────────────────────────────────────┐
│  Syntax         │  Type       │  Meaning                     │
├──────────────────────────────────────────────────────────────┤
│  - [ ] 1. ...   │  Mandatory  │  Must be implemented         │
│  - [ ]* 1. ...  │  Optional   │  Can be skipped for MVP      │
└──────────────────────────────────────────────────────────────┘
```

The asterisk `*` after the closing bracket marks the task as optional. Optional tasks are typically: unit tests, visual polish, or "nice-to-have" features.

---

## Task Hierarchy

```markdown
- [ ] 1. Main task title (epic-level)
  - [ ] 1.1 Sub-task title
    - Implementation detail
    - Implementation detail
    - _Requirements: N.N_

  - [ ] 1.2 Next sub-task
    - Implementation detail
    - _Requirements: N.N, N.N_

- [ ] 2. Next main task
  - [ ] 2.1 Sub-task
```

**Rules:**
- Main tasks: simple numbering (1, 2, 3 …)
- Sub-tasks: compound numbering (1.1, 1.2, 2.1 …)
- Sub-tasks are indented with 2 spaces
- A main task is complete only when ALL its mandatory sub-tasks are complete

---

## Sub-task Anatomy

Each sub-task must contain enough detail for a developer or agent to implement without ambiguity.

```
┌──────────────────────────────────────────────────────────────┐
│  Element                  │  Required?  │  Example           │
├──────────────────────────────────────────────────────────────┤
│  File to create/edit      │  Yes        │  Create `x.ts`     │
│  What to implement        │  Yes        │  Class, function   │
│  Technical details        │  Yes        │  Methods, params   │
│  Concrete values          │  When present│  Colors, texts    │
│  Traceability             │  Yes        │  _Requirements: N_   │
└──────────────────────────────────────────────────────────────┘
```

**Level of detail:**
- Name files with full path (e.g., `server/db/init.ts`)
- List methods/functions with names and parameters
- Include concrete values: hex colors, UI text, HTTP codes, SQL constraints
- Reference patterns to follow: parameterized queries, semantic HTML
- Reference component names from `design.md` exactly as written

---

## Traceability Format

Every sub-task ends with a traceability reference in italic:

```markdown
- _Requirements: N.N_                    # single criterion
- _Requirements: 1.1, 2.3, 5.2_         # multiple criteria
- _Requirements: All_                    # only for test/doc tasks
```

Format: `_Requirements: {requirement_number}.{criterion_number}_`

---

## Good vs Poor Sub-task Examples

**Good:**
```markdown
- [ ] 2.2 Implement TaskRepository
  - Create `server/db/taskRepository.ts` with class `TaskRepository`
  - Implement methods: `findAll()`, `findById(id)`, `create(data)`, `update(id, data)`, `delete(id)`
  - Use parameterized queries to prevent SQL injection
  - Use `RETURNING *` to return the record after INSERT/UPDATE
  - _Requirements: 9.1, 10.1_
```

**Poor:**
```markdown
- [ ] 2.2 Build database stuff
  - Make repository work
  - _Requirements: 9.1_
```

**Good:**
```markdown
- [ ] 5.4 Implement TaskCard component
  - Create `src/components/TaskCard/TaskCard.tsx` and `TaskCard.module.css`
  - Props: `task`, `onEdit`, `onDelete`, `onStatusChange`, `onClick`
  - Priority border-left 4px: red (#D91515) for High, orange (#FF9900) for Medium, gray for Low
  - Action buttons: edit (✏️), delete (🗑️), advance status (➡️) with aria-labels
  - Use `<article>` as root element, border-radius 8px, subtle shadow
  - _Requirements: 2.1, 2.2, 2.3, 2.4_
```
