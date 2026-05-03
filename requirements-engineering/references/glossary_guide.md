# Glossary Guide

The Glossary creates a shared vocabulary that eliminates ambiguity in acceptance criteria. Every term used in a requirement criterion must be defined in the Glossary first.

## Why the Glossary Matters

Without a Glossary:
- "the card", "Task_Card", and "task item" all refer to the same thing — inconsistently
- Stakeholders and developers read the same criterion differently
- Tests cannot be written without interpretive assumptions

With a Glossary, every noun in a criterion maps exactly to one definition.

---

## Naming Convention

Use `Underscore_Case` for composite terms. This makes them behave like identifiers — unambiguous and directly reusable across criteria.

| Informal Name | Glossary Term |
|---|---|
| task card | `Task_Card` |
| edit modal | `Edit_Modal` |
| creation date | `Created_At` |
| REST API | `REST_API` |
| kanban column | `Kanban_Column` |
| status badge | `Status_Badge` |

Single-word terms use standard title case: `Task`, `User`, `Dashboard`, `Status`.

---

## 4 Mandatory Categories

Cover all relevant categories to ensure a complete vocabulary.

### 1. Domain Entities
The primary objects the system manages — what gets created, updated, and deleted.

Examples:
- **Task**: Core entity containing title, description, priority, status, and creation timestamp.
- **User**: Authenticated individual with a unique email, display name, and assigned Role.
- **Comment**: Text entry associated with a single Task, including author reference and creation timestamp.
- **Order**: Commercial transaction linking a User to one or more Products with a total amount and Status.

### 2. UI Components
Screens, pages, visual containers, and interactive elements visible to the user.

Examples:
- **Dashboard**: Main screen that displays all Tasks organized in Kanban_Columns by Status.
- **Task_Card**: Visual component that shows a Task's title, Priority badge, and Status indicator.
- **Edit_Modal**: Overlay dialog that allows the user to modify an existing Task's fields.
- **Kanban_Column**: Vertical section of the Dashboard that groups Task_Cards sharing the same Status.
- **Status_Counter**: Numeric badge at the top of a Kanban_Column showing the count of Task_Cards in that column.

### 3. Technical Components
Interfaces, services, and infrastructure layers referenced in criteria.

Examples:
- **REST_API**: HTTP interface that exposes CRUD operations on system resources.
- **Database**: Persistent storage layer that holds all Task and User records.
- **Auth_Service**: Service responsible for verifying user credentials and issuing session tokens.
- **Email_Service**: External service that sends transactional emails (confirmation, notification, reset).

### 4. States & Enums
Fixed value sets used by domain entities — statuses, priorities, roles.

Examples:
- **Status**: Enumeration of Task lifecycle states — values: `To Do`, `In Progress`, `Done`.
- **Priority**: Enumeration of Task urgency levels — values: `High`, `Medium`, `Low`.
- **Role**: Enumeration of User permission levels — values: `Admin`, `Member`, `Viewer`.

---

## Rules

1. **Every term in a criterion must be in the Glossary** — if you write `THE Task_Card SHALL...`, `Task_Card` must be defined.
2. **Alphabetical order** — makes it easy to scan for duplicates and look up terms quickly.
3. **Exactly 1 sentence per definition** — no ambiguity, no "and also".
4. **No circular definitions** — do not define `Task` as "an item that appears in a Task_Card".
5. **Build the Glossary before writing criteria** — define terms in Step 2 before generating criteria in Step 4.
6. **Only define domain-specific terms** — do not define HTTP, JSON, CRUD, or other general technical vocabulary.

---

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|---|---|---|
| Same concept, two names | `Task` and `Todo_Item` both refer to the same entity | Pick one name and use it everywhere |
| Vague definition | "Task: something the user does." | Specify exact fields, purpose, and constraints |
| Term used in criteria but missing from Glossary | Criterion says `THE Status_Badge SHALL...` but `Status_Badge` is not defined | Add the missing term |
| Over-defining | Defining HTTP, JSON, REST | Only define project-specific and domain-specific terms |
| Circular definition | "`Task_Card`: A card that displays a Task." | Define what it contains and its role in the UI |

---

## Complete Example Glossary

```markdown
## Glossary

- **Auth_Service**: Service responsible for verifying user credentials and issuing JWT session tokens.
- **Comment**: Entity associated with a single Task containing author reference, body text, and creation timestamp.
- **Dashboard**: Main screen that displays all Tasks organized in Kanban_Columns by Status.
- **Database**: PostgreSQL persistence layer that stores all Task, User, and Comment records.
- **Edit_Modal**: Overlay dialog that allows editing an existing Task's fields without leaving the Dashboard.
- **Kanban_Column**: Vertical section of the Dashboard that groups Task_Cards sharing the same Status value.
- **Priority**: Enumeration of Task urgency — values: `High`, `Medium`, `Low`.
- **REST_API**: HTTP interface (Express.js) exposing CRUD endpoints for Task and Comment resources.
- **Role**: Enumeration of User permission levels — values: `Admin`, `Member`, `Viewer`.
- **Status**: Enumeration of Task lifecycle states — values: `To Do`, `In Progress`, `Done`.
- **Status_Counter**: Numeric badge at the top of each Kanban_Column showing the count of Task_Cards in that column.
- **Task**: Core entity containing id, title, description, priority, status, category, due date, and creation timestamp.
- **Task_Card**: Visual component on the Dashboard displaying a Task's title, Priority badge, and Status.
- **Task_Form**: Form component used to create or edit a Task, displayed inside the Edit_Modal.
- **User**: Authenticated individual with a unique email, display name, Role, and active session token.
```
