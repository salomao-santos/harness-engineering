# Identify Edge Cases Guide

Edge cases are the scenarios beyond the happy path — empty inputs, boundary values, failures, unauthorized access, and concurrent usage. Skipping them produces requirements that look complete but fail in production.

---

## The 5-Question Checklist

Apply these questions to every requirement before declaring it done:

| # | Question | What to look for |
|---|---|---|
| 1 | What if the input is empty or null? | Required fields left blank, empty collections, null foreign keys |
| 2 | What if the input is at boundary values? | Min/max length, min/max count, exact file size limits |
| 3 | What if the operation fails? | Network errors, database failures, timeouts, third-party service errors |
| 4 | What if the user is not authorized? | Unauthenticated access, wrong Role, expired session |
| 5 | What if there are concurrent operations? | Two users editing the same record, duplicate submissions, race conditions |

---

## 4 Edge Case Patterns

### 1. Error Handling

```
WHEN {operation fails}, THE {Component} SHALL {display error / offer retry / log}.
```

Example:
```
WHEN the file upload fails due to a network error, THE Upload_Form SHALL display
"Upload failed. Try again." and a Retry button.
```

### 2. Boundary Conditions

```
WHEN {value equals minimum or maximum}, THE {Component} SHALL {specific behavior}.
```

Examples:
```
WHEN the user selects a file over 10MB, THE Upload_Form SHALL display
"File too large (max 10MB)" and SHALL NOT begin the upload.

WHEN the title reaches 255 characters, THE Task_Form SHALL disable
further input in the title field and display "255/255 characters used".
```

### 3. Concurrent Access

```
WHEN {multiple users act on the same resource simultaneously},
THE {System} SHALL {conflict resolution behavior}.
```

Example:
```
WHEN two users attempt to update the same Task simultaneously, THE REST_API
SHALL return HTTP 409 with the message "Task was modified by another user.
Reload and try again."
```

### 4. Empty States

```
IF {collection or list is empty}, THEN THE {Component} SHALL {empty state message}.
```

Examples:
```
IF no Tasks exist in a Kanban_Column, THEN THE Kanban_Column SHALL display
the message "No tasks here yet".

IF the search query returns no results, THEN THE Dashboard SHALL display
"No tasks match your search." with a "Clear search" link.
```

---

## Property-Based Testing (PBT) Patterns

Use the `FOR ALL` construction for any requirement involving data transformation, serialization, or stateful operations. These map directly to property-based test implementations.

| Property Type | Definition | EARS Construction |
|---|---|---|
| **Round-trip** | Encode then decode = original | `FOR ALL valid {objects}, serialize to {format} and deserialize SHALL produce an equivalent object.` |
| **Idempotence** | Apply N times = apply 1 time | `FOR ALL valid {operations}, applying {operation} twice SHALL produce the same result as applying it once.` |
| **Invariant** | A condition always holds | `FOR ALL {objects} in any valid state, {property} SHALL remain within {bounds}.` |
| **Preservation** | Unrelated fields unchanged | `FOR ALL {operations} on {field}, all fields not targeted by the operation SHALL remain unchanged.` |

---

## Complete PBT Examples

```markdown
# Round-trip (JSON serialization)
FOR ALL valid Task objects, serializing to JSON and deserializing back SHALL
produce an object with identical id, title, description, priority, status,
and created_at values.

# Round-trip (API create → retrieve)
FOR ALL valid Task objects, creating a Task via POST /tasks and retrieving it
via GET /tasks/:id SHALL return all submitted fields unchanged.

# Idempotence (status update)
FOR ALL Tasks with Status "Done", updating the Status to "Done" again SHALL
leave the Task in the same state as before the second update.

# Invariant (valid status constraint)
FOR ALL Task objects at any point in their lifecycle, the Status field SHALL
contain exactly one of: "To Do", "In Progress", "Done".

# Preservation (partial update)
FOR ALL PATCH /tasks/:id requests that update only the title field, the
description, priority, status, category, due_date, and created_at fields
SHALL remain unchanged.
```

---

## Edge Cases vs. New Requirements

An edge case becomes a separate requirement when it:
- Involves a distinct User Story (different persona or different motivating value)
- Requires its own set of UI states or user interactions
- Has 3 or more criteria of its own

Otherwise, add it as additional `IF`/`WHEN` criteria within the existing requirement. Keep related behaviors together when the user story is the same.
