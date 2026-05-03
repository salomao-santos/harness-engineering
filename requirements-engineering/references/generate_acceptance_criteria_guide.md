# Generate Acceptance Criteria Guide

Acceptance criteria define exactly when a requirement is satisfied. They are the contract between stakeholders and implementers, and the direct specification for tests.

---

## The 7 EARS Constructions

Use these 7 patterns. Mix them within a single requirement as needed.

| # | Construction | When to Use | Example |
|---|---|---|---|
| 1 | `THE {Component} SHALL {behavior}.` | Default state or base behavior the system always exhibits | `THE Dashboard SHALL display all Tasks ordered by creation date descending.` |
| 2 | `WHEN {event}, THE {Component} SHALL {behavior}.` | System reaction to a user action or system event | `WHEN the user clicks "New Task", THE Dashboard SHALL open the Task_Form with all fields empty.` |
| 3 | `IF {condition}, THEN THE {Component} SHALL {behavior}.` | Validation, error case, or special condition | `IF the title field is empty, THEN THE Task_Form SHALL display the message "Title is required".` |
| 4 | `THE {Component} SHALL NOT {behavior}.` | Restriction or explicitly prohibited behavior | `THE REST_API SHALL NOT expose stack traces or internal error details in production responses.` |
| 5 | `FOR ALL valid {objects}, {operation} SHALL {result}.` | Correctness property (property-based testing) | `FOR ALL valid Task objects, serializing to JSON and deserializing SHALL produce an equivalent object.` |
| 6 | `WHILE {condition}, THE {Component} SHALL {continuous behavior}.` | Behavior that must be maintained during an ongoing operation | `WHILE a file is uploading, THE Upload_Form SHALL display a progress indicator.` |
| 7 | `WHERE {context/environment}, THE {Component} SHALL {behavior}.` | Behavior that applies only in a specific context or environment | `WHERE the user is on a mobile device, THE Dashboard SHALL display a single-column layout.` |

**WHILE** targets async operations and active states (uploading, loading, processing, editing). Use it when the behavior must persist for the entire duration of the condition — not just when it starts.

**WHERE** targets environment- or context-dependent behaviors (device type, deployment mode, multi-user scenarios). Use it when the requirement only applies in a specific operational context.

---

## EARS Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|---|---|---|
| **Compound criterion** | `THE Form SHALL validate fields AND display errors.` | One behavior per criterion — split into two numbered items |
| **Vague trigger** | `WHEN appropriate, THE System SHALL...` | Use observable, unambiguous triggers: user actions, HTTP events, time thresholds |
| **Implementation detail** | `THE System SHALL use Redis to cache the list.` | State the observable outcome: `WHEN the user requests the list, THE Dashboard SHALL respond within 100ms.` |
| **Untestable requirement** | `THE Dashboard SHALL be intuitive.` | Replace subjective terms with measurable criteria: click count, time-to-task, error rate |

---

## 6 Rules for Writing Criteria

1. **Independently testable** — each criterion can be verified in isolation, without depending on another criterion passing first
2. **Reference Glossary terms** — write `THE Task_Card`, not "the card"; `THE Edit_Modal`, not "the popup"
3. **Use concrete values** — specify exact text strings, HTTP codes, sizes in px, colors in hex, times in ms
4. **Cover happy path + error path** — for every action, include what happens on success and what happens on failure
5. **One behavior per criterion** — never combine two behaviors with "and"; split them into separate numbered items
6. **3–8 criteria per requirement** — if you need more than 8, the requirement covers too much and must be split

---

## Concrete Values in Criteria

Replace vague terms with exact specifications:

| Vague | Concrete |
|---|---|
| "an error message" | `"Title is required"` |
| "a success response" | `HTTP 201` |
| "not found response" | `HTTP 404 with JSON body { "error": "Task not found" }` |
| "rounded corners" | `border-radius: 8px` |
| "good contrast" | `minimum contrast ratio of 4.5:1` |
| "fast response" | `response time under 200ms` |
| "a loading indicator" | `a spinner with aria-label="Loading tasks"` |

---

## Requirement Layering Order

Generate requirements in this order to ensure complete coverage. Not every project needs all layers — include only relevant ones.

**Feature requirements (single-service, UI-facing):**

| Order | Layer | What It Covers |
|---|---|---|
| 1 | UI / Layout | Screens, visual components, visual states |
| 2 | User Interaction | CRUD forms, navigation, modals, drag-and-drop |
| 3 | Business Logic | Rules, state transitions, calculations, validations |
| 4 | API / Communication | Endpoints, contracts, HTTP codes, data formats |
| 5 | Data Persistence | Schema, referential integrity, seed data, migrations |
| 6 | Accessibility | Keyboard nav, semantic HTML, contrast, ARIA, focus |
| 7 | Serialization / Correctness | Round-trip, invariants, property-based tests |
| 8 | Error Handling | Messages, fallbacks, HTTP errors, UI error states |

**System requirements (distributed, multi-service, or high-throughput):**

| Order | Layer | What It Covers |
|---|---|---|
| 9 | Performance & SLA | Response time targets (p95/p99), throughput (RPS, events/sec), rate limiting |
| 10 | Availability & Fault Tolerance | Graceful degradation, circuit breaking, failover, uptime targets |
| 11 | Observability | Health checks, metrics retention, alerting thresholds, distributed tracing |
| 12 | Security | Service-to-service authentication, audit logging, data encryption at rest/transit |

Layers 9–12 apply when the system spans multiple services, handles high load, or has explicit SLA commitments. A simple CRUD feature rarely needs them.

---

## Full Worked Example (3 Requirements)

```markdown
### Requirement 1: Kanban Dashboard Layout

**User Story:** As a user, I want to see my tasks organized by status in columns,
so that I have a clear view of my work's progress at a glance.

#### Acceptance Criteria

1. THE Dashboard SHALL display three Kanban_Columns with titles "To Do",
   "In Progress", and "Done".
2. THE Dashboard SHALL position each Task_Card in the Kanban_Column matching
   the Task's Status.
3. THE Dashboard SHALL display a Status_Counter at the top of each Kanban_Column
   showing the count of Task_Cards in that column.
4. THE Dashboard SHALL display a "New Task" button fixed at the top of the page.
5. IF no Task_Cards exist in a Kanban_Column, THEN THE Kanban_Column SHALL
   display the message "No tasks here yet".

---

### Requirement 2: Task Creation

**User Story:** As a user, I want to create new tasks using a form, so that I can
add work items to my board with all relevant details captured upfront.

#### Acceptance Criteria

1. WHEN the user clicks "New Task", THE Dashboard SHALL open the Task_Form
   with all fields empty.
2. THE Task_Form SHALL display input fields for: title (required), description
   (optional), priority selector (High/Medium/Low), and due date picker (optional).
3. WHEN the user fills required fields and confirms, THE REST_API SHALL create
   a new Task with Status "To Do" and return HTTP 201.
4. WHEN the Task is created successfully, THE Dashboard SHALL display the new
   Task_Card in the "To Do" Kanban_Column and increment the Status_Counter by 1.
5. IF the user submits the form with an empty title, THEN THE Task_Form SHALL
   display the message "Title is required" below the title input.
6. THE Task_Form SHALL NOT allow submission while the title field is empty.
7. WHILE THE REST_API is processing the creation request, THE Task_Form SHALL
   disable the submit button and display a loading spinner.

---

### Requirement 3: Task REST API

**User Story:** As a developer, I want a structured REST API, so that the frontend
can perform all CRUD operations on Tasks with predictable responses.

#### Acceptance Criteria

1. THE REST_API SHALL expose: POST /tasks, GET /tasks, GET /tasks/:id,
   PUT /tasks/:id, and DELETE /tasks/:id.
2. WHEN the REST_API receives POST /tasks with a valid body, THE REST_API SHALL
   create a new Task, persist it to the Database, and return HTTP 201 with the
   created Task as JSON.
3. WHEN the REST_API receives POST /tasks without a title, THE REST_API SHALL
   return HTTP 400 with body { "error": "title is required" }.
4. WHEN the REST_API receives a request referencing a non-existent Task id,
   THE REST_API SHALL return HTTP 404 with body { "error": "Task not found" }.
5. IF an internal server error occurs, THEN THE REST_API SHALL return HTTP 500
   with { "error": "Internal server error" } and SHALL NOT expose stack traces.
```
