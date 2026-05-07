---
name: requirements-engineering
description: >
  Transform vague feature ideas into clear, testable requirements documents
  using the EARS (Easy Approach to Requirements Syntax) format. Use when:
  user asks to write requirements, create a spec, define acceptance criteria,
  or document a feature before building it. Trigger phrases: "write requirements
  for", "create a requirements doc", "spec out", "define acceptance criteria",
  "requirements engineering", "write a spec", "document this feature".
  Also use this skill whenever someone wants to define, clarify, or formalize
  what a feature should do before design or coding begins — even if they never
  say the word "requirements".
license: MIT
compatibility: Claude Code, Cursor, VS Code, Windsurf, Kiro, Github Copilot, Antigravity
metadata:
  category: methodology
  complexity: intermediate
  author: Salomão da Silva Santos
  version: "2.0.0"
---

# Requirements Engineering

Transform vague feature ideas into structured, testable requirements before any design or code is written. This skill applies the EARS (Easy Approach to Requirements Syntax) format to produce unambiguous acceptance criteria that serve directly as test specifications.

## When to Use This Skill

- Starting any new feature or system from scratch
- Clarifying an ambiguous stakeholder request
- Creating acceptance criteria for user stories
- Defining system behavior before writing tests or code
- Ensuring shared understanding before design and implementation
- Writing non-functional requirements (performance, availability, observability, security) for distributed or high-throughput systems

---

## 6-Step Workflow

### Step 1: Analyze the Request

Break the feature idea into user-facing behaviors, domain entities, system flows, and constraints. Identify: who uses it, what they need to do, and what the system must enforce. Do not jump to acceptance criteria until the scope is clear.

### Step 2: Build the Glossary

Extract every domain term that will appear in acceptance criteria. Define each in exactly one sentence using `Underscore_Case` for composite terms (e.g., `Edit_Modal`, `REST_API`, `Task_Card`). Cover four categories: Domain Entities, UI Components, Technical Components, States & Enums.

→ See [glossary_guide.md](references/glossary_guide.md) for naming conventions, categories, rules, and examples.

### Step 3: Capture User Stories

Write one User Story per requirement: `As a [role], I want [action], so that [value].` The persona must be specific, the action concrete and verifiable, and the value distinct from the action — not a repetition of it.

→ See [capture_user_stories_guide.md](references/capture_user_stories_guide.md) for the 3-element breakdown, anti-patterns, and examples.

### Step 4: Generate Acceptance Criteria

Write 3–8 criteria per requirement using the 7 EARS constructions (THE...SHALL, WHEN, IF...THEN, SHALL NOT, FOR ALL, WHILE, WHERE). Cover both the happy path and the error path. Use Glossary terms — not informal names. Use concrete values: exact strings, HTTP codes, pixel sizes, hex colors.

→ See [generate_acceptance_criteria_guide.md](references/generate_acceptance_criteria_guide.md) for all 7 constructions, 6 rules, the 8-layer ordering, and a worked example.

### Step 5: Identify Edge Cases & Correctness Properties

For each requirement ask: What if input is empty? At boundary values? If the operation fails? If the user is unauthorized? If concurrent? Add `FOR ALL` criteria for any data transformation or serialization.

→ See [identify_edge_cases_guide.md](references/identify_edge_cases_guide.md) for the 5-question checklist, 4 edge case patterns, and Property-Based Testing (PBT) examples.

### Step 6: Validate & Deliver

Run the coverage checklist: every feature has a requirement, every domain term is in the Glossary, every criterion uses a formal construction, error paths are covered, no requirement exceeds 8 criteria. Present to the user for review and iterate until approved.

→ See [validate_requirements_guide.md](references/validate_requirements_guide.md) for the full checklist, 4-dimension validation, common pitfalls, and the design-phase handoff checklist.
→ See [checklist_guide.md](references/checklist_guide.md) for the complete Requirements Phase Checklist (gathering, review, validation, and stakeholder sign-off).

---

## Quick Example

```markdown
## Glossary
- **Dashboard**: Main screen that lists all Tasks for the user.
- **Task**: Core entity containing title, description, priority, status, and creation date.
- **Task_Form**: Form component for creating or editing a Task.

## Requirements

### Requirement 1: Task List Display

**User Story:** As a user, I want to see all my tasks in a list, so that I can quickly find the one I need.

#### Acceptance Criteria
1. THE Dashboard SHALL display all Tasks ordered by creation date descending.
2. THE Dashboard SHALL show the title and first 100 characters of description for each Task.
3. IF no Tasks exist, THEN THE Dashboard SHALL display the message "No tasks found".

### Requirement 2: Task Creation

**User Story:** As a user, I want to create new tasks using a form, so that I can organize my work into trackable items.

#### Acceptance Criteria
1. WHEN the user clicks "New Task", THE Dashboard SHALL open the Task_Form with all fields empty.
2. THE Task_Form SHALL display fields for title (required), description, and priority (High/Medium/Low).
3. WHEN the user fills required fields and confirms, THE API SHALL create the Task and return HTTP 201.
4. IF the title field is empty, THEN THE Task_Form SHALL display the message "Title is required".
5. WHILE THE REST_API is processing the request, THE Task_Form SHALL disable the submit button and display a loading spinner.
6. FOR ALL valid Task objects, serializing to JSON and deserializing SHALL produce an equivalent object.
```

---

## Checklist

Before delivering, run the checklist in [checklist_guide.md](references/checklist_guide.md).

### Quick Reference
- [ ] Clear introduction and problem statement
- [ ] Glossary with all domain terms in `Underscore_Case`
- [ ] User stories: specific role, concrete action, distinct benefit
- [ ] EARS-formatted acceptance criteria (3–8 per requirement)
- [ ] Happy path **and** error path covered for each requirement
- [ ] No vague language; error paths include HTTP codes, messages, or UI states
- [ ] All stakeholders have reviewed and approved

---

## Output

Use the blank template at [assets/requirements-template.md](assets/requirements-template.md) as the starting point for a new requirements document.

For complete worked examples across all requirement types (UI Layout, CRUD, API, Accessibility, PBT), see [sample_requirements_guide.md](references/sample_requirements_guide.md).

---

## Output Locations

### Primary file (all tools)

Always generate the requirements document at:

```
<workspace-root>/docs/specs/{feature-name}/requirements.md
```

### Secondary file (tool-specific)

After generating the primary file, generate **one** secondary file for the active tool only. The secondary file must **reference** the primary — it is not a copy.

#### How to determine the active tool

1. **Detect automatically** — check for tool-specific directories in the workspace root:
   - `.kiro/` present → **Kiro**
   - `.github/` present → **GitHub Copilot**
   - `.agents/` present → **Google Antigravity**
   - `.claude/` present → **Claude**
2. **Multiple matches or none found** — ask the user: _"Which tool are you using? (Kiro / GitHub Copilot / Google Antigravity / Claude)"_
3. **User already stated the tool** in the request — use that, skip detection.

Generate the secondary file **only for the detected/chosen tool**. Do not create folders or files for the others.

| Tool | Secondary file path | Content |
|------|---------------------|---------|
| **Kiro** | `<workspace-root>/.kiro/specs/{feature-name}/memory-bank.md` | Reference to `docs/specs/{feature-name}/requirements.md` |
| **GitHub Copilot** | `<workspace-root>/.github/prompts/prompt-requirements-{feature-name}.md` | Brief prompt that instructs Copilot to use `docs/specs/{feature-name}/requirements.md` |
| **Google Antigravity** | `<workspace-root>/.agents/prompts/prompt-requirements-{feature-name}.md` | Brief prompt that instructs Antigravity to use `docs/specs/{feature-name}/requirements.md` |
| **Claude** | `<workspace-root>/.claude/prompts/prompt-requirements-{feature-name}.md` | Brief prompt that instructs Claude to use `docs/specs/{feature-name}/requirements.md` |

#### Kiro — `memory-bank.md` format

```markdown
# Requirements — {Feature Name}

> Source of truth: [docs/specs/{feature-name}/requirements.md](../../docs/specs/{feature-name}/requirements.md)

This memory-bank entry points to the approved requirements document for **{Feature Name}**.
Refer to the linked file for the full Glossary, User Stories, and Acceptance Criteria.
```

#### GitHub Copilot — prompt file format

```markdown
# Prompt: Requirements for {Feature Name}

Use the requirements document located at:
`docs/specs/{feature-name}/requirements.md`

When implementing or reviewing code related to **{Feature Name}**, load that file for the
full Glossary, User Stories, and Acceptance Criteria before suggesting any changes.
```

#### Google Antigravity — prompt file format

```markdown
# Prompt: Requirements for {Feature Name}

Use the requirements document located at:
`docs/specs/{feature-name}/requirements.md`

When implementing or reviewing code related to **{Feature Name}**, load that file for the
full Glossary, User Stories, and Acceptance Criteria before suggesting any changes.
```

#### Claude — prompt file format

```markdown
# Prompt: Requirements for {Feature Name}

Use the requirements document located at:
`docs/specs/{feature-name}/requirements.md`

When implementing or reviewing code related to **{Feature Name}**, load that file for the
full Glossary, User Stories, and Acceptance Criteria before suggesting any changes.
```
