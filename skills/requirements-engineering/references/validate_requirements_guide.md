# Validate Requirements Guide

Run this validation before presenting the requirements document to the user. It ensures complete coverage, correct format, and readiness for the design phase.

---

## Coverage Checklist

Work through each item sequentially.

- [ ] **Every described feature has at least 1 requirement** — if the user mentioned a feature, there is a requirement for it
- [ ] **Every domain term used in criteria is defined in the Glossary** — scan each criterion for nouns; look each up in the Glossary
- [ ] **Every requirement has a User Story with distinct persona, action, and value** — the "so that" clause must add information beyond the "I want" clause
- [ ] **Every criterion uses one of the 7 formal EARS constructions** — THE...SHALL, WHEN...SHALL, IF...THEN...SHALL, SHALL NOT, FOR ALL, WHILE...SHALL, WHERE...SHALL
- [ ] **Error paths are covered** — for every happy-path criterion, there is at least one error-path criterion
- [ ] **Correctness properties are defined for all data transformations** — any serialization, round-trip, or mutation has a FOR ALL criterion
- [ ] **Accessibility requirements are present** *(if UI exists)* — keyboard navigation, semantic HTML, ARIA labels, visible focus indicators, contrast ratio
- [ ] **No requirement exceeds 8 criteria** — if it does, split it into two requirements
- [ ] **Numbering is sequential with no gaps** — Requirement 1, 2, 3... no skipped numbers, no duplicates

---

## 4-Dimension Validation (+ IEEE 830)

### Completeness
- [ ] All user roles identified and addressed
- [ ] Normal flow (happy path) covered for every feature
- [ ] Edge cases documented
- [ ] Error states handled
- [ ] Business rules captured

### Clarity
- [ ] Each criterion uses precise, unambiguous language
- [ ] No vague terms: "fast", "user-friendly", "easy", "good", "intuitive"
- [ ] Exact values specified (HTTP codes, px, ms, hex colors, exact strings)
- [ ] Glossary terms used consistently — no synonyms for the same concept

### Consistency
- [ ] Exactly one of the 7 EARS constructions used per criterion
- [ ] Same terminology used for the same concept across all requirements
- [ ] No contradictory requirements (check pairs that touch the same feature)
- [ ] Similar scenarios handled with similar constructions

### Testability
- [ ] Each criterion can be verified through automated or manual testing
- [ ] Success conditions are observable (a UI state, an HTTP code, a database record)
- [ ] Inputs and expected outputs are specified
- [ ] Performance criteria are measurable (not "fast" but "under 200ms")

### Ranked *(IEEE 830)*
- [ ] Requirements are prioritized — indicate Must Have / Should Have / Nice to Have, or use MoSCoW notation
- [ ] High-priority requirements are complete before lower-priority ones are elaborated
- [ ] Stakeholders have agreed on the priority order before handoff to Design

### Traceable *(IEEE 830)*
- [ ] Each requirement traces back to a stated user need, business rule, or stakeholder request
- [ ] No requirement exists without a motivating User Story
- [ ] If a project management tool is used (Linear, Jira, GitHub Issues), each requirement maps to a ticket or epic

---

## 5 Common Pitfalls + Fixes

### Pitfall 1: Vague Requirement
**Bad:** `THE System SHALL respond quickly.`

**Fix:** `WHEN the user submits a search query, THE REST_API SHALL return results within 200ms for result sets up to 1000 Tasks.`

### Pitfall 2: Implementation Detail in Requirement
**Bad:** `THE System SHALL use Redis to cache the Task list.`

**Fix:** `WHEN the user requests the Task list, THE Dashboard SHALL display results within 100ms for lists up to 1000 Tasks.`

### Pitfall 3: Missing Error Case
**Bad:** Only happy path: `WHEN the user fills the form and confirms, THE REST_API SHALL create the Task.`

**Fix:** Add: `IF the title is empty, THEN THE Task_Form SHALL display "Title is required".`

### Pitfall 4: Untestable Requirement
**Bad:** `THE Dashboard SHALL be intuitive to use.`

**Fix:** `WHEN a new user completes onboarding, THE Dashboard SHALL require no more than 3 clicks to create the first Task.`

### Pitfall 5: Conflicting Requirements
**Bad:** Requirement 3 says Tasks are immutable after "Done" status. Requirement 7 says admins can edit any Task.

**Fix:** Add an explicit exception: `IF the User has Role "Admin", THEN THE Edit_Modal SHALL allow editing Tasks with Status "Done".`

---

## When to Split a Requirement

Split a requirement if any of these are true:

- More than 8 criteria are needed
- Two distinct personas drive it
- The User Story has two separate values (two "so that" clauses)
- The happy path and the error path are each complex enough to stand alone

**Splitting pattern:**
- Requirement N: Task Creation (happy path)
- Requirement N+1: Task Creation — Validation and Error Handling

---

## Complex Systems — Additional Checklist

Apply these checks when requirements cover distributed, multi-service, or high-throughput systems. They address the most common underspecification patterns in complex architectures.

- [ ] **Performance targets are quantified** — no "fast" or "scalable"; use exact numbers: response time in ms (p95/p99), throughput in RPS or events/second, max concurrent users
- [ ] **Uptime and availability targets are stated** — percentage (e.g., 99.9%) and how it is measured (Health_Check polling, synthetic monitoring)
- [ ] **Fault tolerance behavior is fully specified** — define what happens when each component fails: cached response, circuit break, queue + retry, degraded mode
- [ ] **Service-to-service security is covered** — authentication between internal services is explicitly required, not just user-facing auth
- [ ] **Observability requirements exist** — health check endpoints, metrics retention period, alert thresholds, and distributed tracing propagation are defined
- [ ] **Data retention and recovery are quantified** — minimum retention period, backup frequency, Recovery Time Objective (RTO), and Recovery Point Objective (RPO)
- [ ] **Eventual consistency is acknowledged** — if async/event-driven communication is used, the acceptable consistency window and conflict resolution strategy are stated
- [ ] **Rate limiting behavior is specified** — limit per client, enforcement scope (per service or at gateway), and the exact error response when the limit is exceeded

> These checks come from recurring underspecification patterns observed across distributed system implementations. Each represents a requirement class that appears obvious during development but is expensive to retrofit in production.

---

## Automation Options

Manual validation covers the most important checks; automate the mechanical ones if the team maintains a spec repository.

| What to automate | Tool / Approach |
|---|---|
| EARS format validation | Python script using regex on `SHALL`, `WHEN`, `IF`, `WHILE`, `WHERE` patterns |
| Glossary term coverage | Script that finds nouns in criteria and checks them against the Glossary section |
| Requirements-to-tickets traceability | GitHub Actions workflow that maps requirement IDs to open issues or epics |
| Spec file structure | Pre-commit git hook that validates required sections exist in requirements.md |

For version-controlled specs, store documents under `.kiro/specs/{feature-name}/requirements.md` and run validation on every pull request touching that path.

---

## Handoff Checklist

Before moving to the Design phase, confirm:

- [ ] The user has reviewed the requirements document
- [ ] The user has explicitly approved it, or all feedback has been incorporated
- [ ] All open questions are resolved or documented as out-of-scope
- [ ] Each requirement traces to a feature the user described
- [ ] The document is complete enough to design a system against it without re-questioning the user

The requirements document is the contract. Do not proceed to Design until it is approved.
