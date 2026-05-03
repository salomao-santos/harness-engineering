# Capture User Stories Guide

A User Story defines WHO wants WHAT and WHY. It is the motivational anchor of each requirement — it explains the human need before the system behaviors that satisfy it are listed.

## Format

```
As a [role], I want [action], so that [value].
```

Every User Story has exactly 3 elements: persona, action, and value.

---

## INVEST Quality Check

Before finalizing a User Story, verify it passes the INVEST criteria:

| Criterion | Test | Fail signal |
|---|---|---|
| **Independent** | Can this be developed without completing another story first? | Story says "after the login story is done…" |
| **Negotiable** | Are the details open for discussion with stakeholders? | Story reads like a rigid implementation spec |
| **Valuable** | Does it deliver real benefit to the user or business? | Story exists only for internal/technical reasons |
| **Estimable** | Is the scope clear enough to size for a sprint? | Story is so vague it cannot be broken into tasks |
| **Small** | Can it be completed in a single iteration? | Story covers multiple features or user journeys |
| **Testable** | Will the acceptance criteria be verifiable? | Story contains subjective or unmeasurable outcomes |

A story that fails **Testable** cannot produce valid EARS criteria — rewrite the value clause until the outcome is measurable.

---

## 3-Element Breakdown

| Element | Rule | Bad Example | Good Example |
|---|---|---|---|
| **Role (Persona)** | Specific role in the system — never "a user" generically | "As a person..." | "As a returning customer..." |
| **Action** | Concrete verb + what they want to accomplish — must be verifiable | "...manage my tasks..." | "...create tasks using a form with title and priority..." |
| **Value** | The real "why" — the benefit, not a repetition of the action | "...so that I can create tasks." | "...so that I can organize my work into prioritized, trackable items." |

---

## Anti-Pattern vs. Correct Pattern

**Anti-pattern — value repeats the action:**
> As a user, I want to create tasks, so that I can create tasks.

The "so that" clause adds no information.

**Correct — value explains the benefit:**
> As a user, I want to create tasks by filling out a form, so that I can organize my work into trackable items with priority and status.

The "so that" clause must answer: *why does this matter to the persona?*

---

## Persona Rules

- **Be specific:** `returning customer` beats `user`; `system administrator` beats `admin`; `API consumer` beats `developer`
- **One persona per story:** do not write "As a user or admin" — split into two requirements
- **Use the same persona name consistently:** if you call them `member` in Requirement 1, don't call them `team member` in Requirement 5
- **Match personas to your Glossary** if you have a `User` or `Role` entity defined

Common personas by domain:

| Domain | Example Personas |
|---|---|
| SaaS / Productivity | `user`, `team administrator`, `workspace owner`, `guest user` |
| E-commerce | `returning customer`, `guest shopper`, `store manager` |
| Developer tools | `API consumer`, `developer`, `system integrator` |
| Healthcare | `patient`, `clinician`, `billing administrator` |
| Accessibility | `user with limited mobility`, `screen reader user` |

---

## Action Rules

- Use a concrete verb: `create`, `view`, `filter`, `drag`, `upload`, `receive`, `export`, `search`
- Avoid vague verbs: `manage`, `handle`, `deal with`, `use`, `interact with`
- Include the object: not just "upload" but "upload a file under 10MB"
- The action must be observable and testable

---

## Value Rules

- Explain the business or personal benefit
- Never repeat the action (see anti-pattern above)
- The value guides what the acceptance criteria must ultimately satisfy
- Start with "so that I can [goal]" or "so that [outcome happens]"

---

## Complete Examples

```markdown
# UI / Display
As a user, I want to see all my tasks organized by status in columns, so that I
have a clear view of my work's progress without manually sorting anything.

# CRUD / Interaction
As a user, I want to create new tasks by filling out a form, so that I can add
work items to my board with all relevant details captured upfront.

# State Transition
As a user, I want to move tasks between columns by dragging them, so that I can
update task status without opening an edit dialog every time.

# API / Technical
As a developer, I want a REST API with well-defined HTTP status codes, so that
I can build a reliable frontend without guessing what error responses to handle.

# Accessibility
As a user with limited mobility, I want the full interface to be keyboard-navigable,
so that I can use the application without depending on a mouse.

# Data Integrity
As a developer, I want task data to survive serialization round-trips intact,
so that the API never silently corrupts field values between frontend and database.
```

---

## Common Mistakes

| Mistake | Example | Fix |
|---|---|---|
| Vague persona | "As a person..." | "As a registered user..." |
| Value = action | "...so that I can create tasks" after "I want to create tasks" | Explain the outcome, not the repetition |
| Multi-persona in one story | "As a user or admin..." | Split into two separate requirements |
| Hypothetical value | "...so that maybe I can..." | Value must be concrete and real |
| Implementation leak in value | "...so that the database stores the record" | Value belongs to the user, not the system internals |
| No value clause | "As a user, I want to see my tasks." | Always complete the "so that" clause |
