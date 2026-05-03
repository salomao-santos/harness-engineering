# Sample Requirements Guide

Complete examples across all requirement types.

---

## Example 1: UI Layout

**Context:** Kanban task management board.

### Requirement 1: Kanban Dashboard Layout

**User Story:** As a user, I want to see my tasks organized by status in columns, so that I have a clear view of my work's progress at a glance.

#### Acceptance Criteria

1. THE Dashboard SHALL display three Kanban_Columns with titles "To Do", "In Progress", and "Done".
2. THE Dashboard SHALL position each Task_Card in the Kanban_Column matching the Task's Status.
3. THE Dashboard SHALL display a Status_Counter at the top of each Kanban_Column showing the count of Task_Cards in that column.
4. THE Dashboard SHALL display a "New Task" button fixed at the top of the page.
5. IF no Task_Cards exist in a Kanban_Column, THEN THE Kanban_Column SHALL display the message "No tasks here yet".

---

## Example 2: CRUD Interaction

**Context:** Task creation via a form.

### Requirement 2: Task Creation

**User Story:** As a user, I want to create new tasks using a form, so that I can add work items to my board with all relevant details captured upfront.

#### Acceptance Criteria

1. WHEN the user clicks "New Task", THE Dashboard SHALL open the Task_Form with all fields empty.
2. THE Task_Form SHALL display input fields for: title (required), description (optional), priority selector (High/Medium/Low), category (optional), and due date picker (optional).
3. WHEN the user fills required fields and confirms, THE REST_API SHALL create a new Task with Status "To Do" and return HTTP 201.
4. WHEN the Task is created successfully, THE Dashboard SHALL display the new Task_Card in the "To Do" Kanban_Column and increment the Status_Counter by 1.
5. IF the user submits the form with an empty title, THEN THE Task_Form SHALL display the message "Title is required" directly below the title input.
6. THE Task_Form SHALL NOT allow submission while the title field is empty.

---

## Example 3: State Transition (Drag-and-Drop)

**Context:** Moving tasks between columns by dragging.

### Requirement 3: Task Status Update via Drag-and-Drop

**User Story:** As a user, I want to move tasks between columns by dragging them, so that I can update status without opening an edit dialog every time.

#### Acceptance Criteria

1. WHEN the user starts dragging a Task_Card, THE Dashboard SHALL display visual drop zones on all Kanban_Columns.
2. WHEN the user drops a Task_Card into a different Kanban_Column, THE REST_API SHALL update the Task's Status to match the target column and return HTTP 200.
3. WHEN the status update succeeds, THE Dashboard SHALL move the Task_Card to the target Kanban_Column and update both affected Status_Counters.
4. WHEN the status update fails, THE Dashboard SHALL return the Task_Card to its original Kanban_Column and display the message "Could not update status. Try again."
5. FOR ALL Task status transitions, updating Status from any value to the same value SHALL leave the Task unchanged.

---

## Example 4: REST API

**Context:** Backend API for task management.

### Requirement 4: Task REST API

**User Story:** As a developer, I want a well-structured REST API, so that the frontend can perform all CRUD operations on Tasks with predictable responses.

#### Acceptance Criteria

1. THE REST_API SHALL expose: `POST /tasks`, `GET /tasks`, `GET /tasks/:id`, `PUT /tasks/:id`, and `DELETE /tasks/:id`.
2. WHEN the REST_API receives `POST /tasks` with a valid body, THE REST_API SHALL create a new Task, persist it to the Database, and return HTTP 201 with the created Task as JSON.
3. WHEN the REST_API receives `POST /tasks` without a title field, THE REST_API SHALL return HTTP 400 with body `{ "error": "title is required" }`.
4. WHEN the REST_API receives any request referencing a non-existent Task id, THE REST_API SHALL return HTTP 404 with body `{ "error": "Task not found" }`.
5. IF an internal server error occurs, THEN THE REST_API SHALL return HTTP 500 with `{ "error": "Internal server error" }` and SHALL NOT expose stack traces or database details.

---

## Example 5: Data Correctness (Property-Based Testing)

**Context:** Ensuring data integrity across serialization and partial updates.

### Requirement 5: Task Data Serialization Correctness

**User Story:** As a developer, I want task data to survive serialization intact, so that the API never silently corrupts field values between the frontend and the database.

#### Acceptance Criteria

1. THE REST_API SHALL serialize Task objects as JSON containing all fields: id, title, description, priority, category, due_date, status, and created_at.
2. THE REST_API SHALL deserialize incoming JSON request bodies into valid Task objects before persistence.
3. FOR ALL valid Task objects, serializing to JSON and deserializing back SHALL produce an object with all field values equal to the original.
4. FOR ALL `PATCH /tasks/:id` requests that update only the title, the description, priority, status, category, due_date, and created_at fields SHALL remain unchanged.
5. FOR ALL Task objects at any point in their lifecycle, the Status field SHALL contain exactly one of: `"To Do"`, `"In Progress"`, `"Done"`.
6. IF the REST_API receives a malformed JSON request body, THEN THE REST_API SHALL return HTTP 400 with `{ "error": "Invalid JSON format" }`.

---

## Example 6: Accessibility

**Context:** Keyboard navigation and inclusive design.

### Requirement 6: Keyboard Navigation and Accessibility

**User Story:** As a user with limited mobility, I want the full interface to be keyboard-navigable, so that I can use the application without depending on a mouse.

#### Acceptance Criteria

1. THE Dashboard SHALL be fully operable using keyboard alone: Tab to move between interactive elements, Enter to activate buttons, Escape to close modals.
2. THE Dashboard SHALL use semantic HTML elements: `<main>` for the board area, `<section>` for each Kanban_Column, `<article>` for each Task_Card.
3. THE Dashboard SHALL maintain a minimum color contrast ratio of 4.5:1 for all text against its background, per WCAG 2.1 AA.
4. THE Dashboard SHALL display a visible focus indicator (2px solid `#0066CC` outline) on all interactive elements when focused via keyboard.
5. WHEN a button contains only an icon with no visible text, THE Dashboard SHALL include an `aria-label` attribute describing the action (e.g., `aria-label="Delete task"`).
6. THE Task_Form SHALL announce validation error messages to screen readers using `role="alert"` on the error message elements.

---

## Example 7: Non-Functional Requirements (Performance & Availability)

**Context:** Distributed API system with multiple microservices behind a gateway.

**Glossary additions needed:**
- **API_Gateway**: Single entry point that routes all client requests to downstream Services, applying rate limiting, authentication, and circuit breaking.
- **Circuit_Breaker**: Component within the API_Gateway that halts calls to a Service after a configured failure threshold, preventing cascade failures.
- **Correlation_ID**: Unique request identifier generated at the API_Gateway and propagated to all Services via HTTP headers for distributed tracing.
- **Health_Check**: HTTP endpoint (`GET /health`) exposed by each Service that returns its current operational status as JSON.
- **Service**: Independently deployable microservice (e.g., User Service, Content Service, Notification Service).

### Requirement 7: API Latency, Rate Limiting, and Fault Tolerance

**User Story:** As a system architect, I want the API to meet defined latency and rate limits, so that all clients receive consistent, predictable response times under expected load.

#### Acceptance Criteria

1. WHEN THE API_Gateway receives any GET request, THE REST_API SHALL respond within 200ms at the 95th percentile under normal load (up to 1,000 concurrent users).
2. WHEN THE API_Gateway receives any POST or PUT request, THE REST_API SHALL respond within 500ms at the 95th percentile under normal load.
3. WHERE the deployment environment is production, THE API_Gateway SHALL enforce a rate limit of 1,000 requests per minute per authenticated client and return HTTP 429 with `{ "error": "Rate limit exceeded" }` when the limit is reached.
4. WHILE a Service is returning HTTP 5xx at a rate above 50% over a 10-second sliding window, THE Circuit_Breaker SHALL be open and THE API_Gateway SHALL return HTTP 503 with `{ "error": "Service temporarily unavailable" }` instead of forwarding the request.
5. WHEN THE Circuit_Breaker transitions from open to closed, THE API_Gateway SHALL log the event with the Service name, recovery timestamp, and total downtime duration.
6. THE REST_API SHALL maintain 99.9% monthly uptime measured as the ratio of successful Health_Check responses to total Health_Check polls.
7. FOR ALL requests entering THE API_Gateway, THE API_Gateway SHALL attach a unique Correlation_ID to the forwarded request headers before calling any downstream Service.

---

## Example 8: Observability and Monitoring

**Context:** Monitoring layer for the same distributed API system.

**Glossary additions needed:**
- **Monitoring_Service**: Infrastructure component that collects metrics from all Services, evaluates alert thresholds, and dispatches notifications to on-call channels.

### Requirement 8: Health Monitoring and Automated Alerting

**User Story:** As a system administrator, I want automated health monitoring and alerts, so that I can detect and respond to degradation before users are impacted.

#### Acceptance Criteria

1. THE API_Gateway SHALL expose `GET /health` returning HTTP 200 with a JSON body listing the status and last-known latency of each downstream Service.
2. WHEN a Service returns HTTP 5xx for 3 or more consecutive requests, THE Monitoring_Service SHALL emit an alert containing the Service name, failure count, and timestamp.
3. WHEN the 90th-percentile response time across any Service exceeds 1,000ms for two consecutive minutes, THE Monitoring_Service SHALL emit a latency degradation alert.
4. IF a Service's Health_Check returns a non-200 status for more than 30 consecutive seconds, THEN THE Monitoring_Service SHALL escalate to the on-call notification channel.
5. THE Monitoring_Service SHALL retain all metrics at 60-second resolution for a minimum of 30 days.
6. THE Monitoring_Service SHALL NOT include individual user data or request payloads in any alert notification or exported metric.

---

## Minimal Complete Document (Reference)

```markdown
# Requirements Document — Personal Notes App

## Introduction

This document defines the requirements for a personal notes application.
The system allows users to create, edit, and delete plain-text notes.
The architecture uses React on the frontend, a Node.js REST API, and SQLite for persistence.
This document covers the MVP feature set: note CRUD and basic search.

## Glossary

- **Dashboard**: Main screen that lists all Notes for the authenticated User.
- **Note**: Core entity containing id, title, body text, and creation timestamp.
- **Note_Editor**: Form component for creating or editing a Note.
- **REST_API**: HTTP interface built with Express.js exposing CRUD endpoints for Note resources.

## Requirements

### Requirement 1: Note List Display

**User Story:** As a user, I want to see all my notes in a list ordered by recency, so that
I can quickly find the most recent note without searching.

#### Acceptance Criteria

1. THE Dashboard SHALL display all Notes ordered by creation timestamp descending.
2. THE Dashboard SHALL display the title and first 100 characters of the body for each Note.
3. IF no Notes exist, THEN THE Dashboard SHALL display the message "No notes yet. Create your first note."

### Requirement 2: Note Creation

**User Story:** As a user, I want to create new notes with a title and body, so that I can
record important information in a structured and retrievable way.

#### Acceptance Criteria

1. WHEN the user clicks "New Note", THE Dashboard SHALL open the Note_Editor with all fields empty.
2. THE Note_Editor SHALL display a title field (required) and a body text area (optional).
3. WHEN the user fills the title and confirms, THE REST_API SHALL create the Note and return HTTP 201.
4. WHEN the Note is created, THE Dashboard SHALL display it at the top of the list.
5. IF the title is empty, THEN THE Note_Editor SHALL display "Title is required".
6. FOR ALL valid Note objects, serializing to JSON and deserializing SHALL produce an equivalent object.
```
