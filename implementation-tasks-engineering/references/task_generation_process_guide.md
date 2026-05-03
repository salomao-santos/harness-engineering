# Task Generation Process Guide

Tasks are **never** generated in a vacuum. Every task is derived from two source documents.

---

## Required Inputs

| Document | Extract | How it influences tasks |
|----------|---------|------------------------|
| `requirements.md` | User stories, acceptance criteria, EARS constructions, correctness properties, Glossary | Defines WHAT to implement and the DONE criteria |
| `design.md` | Architecture, components, interfaces, SQL schema, API endpoints, TypeScript types, diagrams, directory structure, seed data | Defines HOW to implement and in which files/modules |

---

## Extraction from requirements.md

For each requirement, extract:

```
┌──────────────────────────────────────────────────────────────┐
│  Extracted element         │  Use in task generation         │
├──────────────────────────────────────────────────────────────┤
│  User Story                │  Task context                   │
│  Acceptance Criteria       │  Define sub-tasks and DONE      │
│  SHALL/WHEN constructions  │  Become implementable steps     │
│  FOR ALL properties        │  Become PBT tasks               │
│  Glossary terms            │  Component/file names           │
└──────────────────────────────────────────────────────────────┘
```

---

## Extraction from design.md

For each design section, extract:

```
┌──────────────────────────────────────────────────────────────┐
│  Design section            │  Use in task generation         │
├──────────────────────────────────────────────────────────────┤
│  Design decisions          │  Technologies to use in tasks   │
│  Architecture diagram      │  Phase ordering                 │
│  UI components             │  Frontend tasks (1 per component)│
│  API endpoints             │  Backend tasks (per resource)   │
│  SQL schema                │  Database tasks                 │
│  TypeScript types          │  Types setup task               │
│  Serialization mapping     │  PBT round-trip tasks           │
│  Seed data                 │  Seed data task                 │
│  Directory structure       │  File paths in tasks            │
│  Requirements traceability │  Task → requirement mapping     │
└──────────────────────────────────────────────────────────────┘
```

---

## Design Artifact → Task Mapping

| Design artifact | Generates task type |
|----------------|---------------------|
| TypeScript interfaces/types | Setup task: create types file |
| SQL table definition | Data layer task: schema init + repository |
| REST API endpoint | Backend task: route handler |
| UI component with props | Frontend task: `.tsx` + `.module.css` |
| Serialization/deserialization mapping | PBT task: round-trip test |
| Seed data specification | Data layer task: seed function |
| CSS design tokens/variables | Frontend task: CSS global variables |
| Error response format | Backend task: error handler |
| Authentication/authorization | Backend task: middleware |
| External service integration | Integration task: API client |

---

## Fundamental Rules

1. **If `design.md` defines a component `TaskCard` with props `task, onEdit, onDelete`**, the corresponding task MUST mention those exact names, props, and file path.

2. **If a requirement has a `FOR ALL` property** (e.g., "FOR ALL valid Task objects, serializing and deserializing SHALL produce an equivalent object"), it MUST become a PBT task in Phase 9.

3. **If `design.md` specifies a directory structure**, all file paths in tasks MUST use those exact paths.

4. **Every requirement must be addressed by at least one task.** Run a coverage check: list all requirement numbers and confirm each appears in at least one `_Requirements:` reference.

5. **Every task must be traceable to at least one requirement.** Tasks without a `_Requirements:` reference are orphaned — either delete them or find the requirement they satisfy.
