# Product Overview Guide (`product.md`)

## Purpose

`product.md` tells AI tools the **why** behind the project. When an AI understands what the product does and who uses it, it makes better trade-off decisions — preferring correctness over cleverness for a financial app, or speed over perfection for a developer tool.

## Section Breakdown

### Product Overview (required)

One or two sentences. What problem does this solve? For whom?

**Good:**
> Task management SaaS for software teams. Replaces status meetings by making every task, its owner, and its current state visible in real time.

**Bad:**
> This is a modern, cutting-edge platform that leverages the latest technologies to deliver an exceptional user experience.

Avoid: superlatives, marketing language, vague adjectives.

### Target Users (required)

Specific personas, not generic "users". Each persona gets a one-line description of what they do with the product.

**Good:**
```markdown
- Software engineer: manages personal backlog, reviews PRs linked to tasks
- Tech lead: assigns tasks, monitors team throughput, exports sprint data
- Product manager: views progress dashboards, writes acceptance criteria in task comments
```

**Bad:**
```markdown
- Users who want to manage tasks
- Admins
```

### Key Features (required)

3–7 features. Each is a concrete capability the product delivers — not an implementation detail. Write from the user's perspective.

**Good:**
```markdown
1. Kanban board with drag-and-drop reordering
2. Task comments with @mentions and email notifications
3. GitHub PR auto-link when branch name contains task ID
4. CSV export for sprint retrospective data
5. Role-based access: owner / member / viewer per project
```

**Bad:**
```markdown
1. Database-backed persistence
2. React frontend
3. REST API
```

### Business Rules & Constraints (required)

Domain rules that govern product behavior. These are the guardrails AI tools must respect when generating product-related logic.

**Format:** plain declarative sentences. Start with the constraint, not the rationale.

```markdown
- Free tier: maximum 3 projects, 10 tasks each; no CSV export
- Paid tier: unlimited projects and tasks; Stripe billing
- Task data must not cross region boundaries (EU tenants isolated from US tenants)
- Deleted tasks are soft-deleted and retained for 30 days; then purged permanently
- Users can only see projects they are members of
```

### Success Metrics (optional)

When the AI knows what "good" looks like from a product perspective, it can make better implementation trade-offs.

```markdown
- P95 task-list load time < 300 ms
- Zero data leakage between tenants
- Kanban board works offline and syncs on reconnect
```

## Common Anti-Patterns

| Anti-pattern | Problem | Fix |
|-------------|---------|-----|
| "Users can do anything they need" | No constraint = AI makes up rules | List the actual 3–7 features |
| "We follow best practices" | Not actionable | State the specific practice |
| "High-performance and scalable" | Vague | "P95 API response < 200 ms under 1000 RPS" |
| Listing tech decisions in product.md | Wrong layer | Move to `tech.md` |
| Missing business rules | AI ignores domain constraints | Add explicit rules with concrete values |

## Length Target

- Product overview: 2–4 sentences
- Target users: 2–5 personas, 1 line each
- Key features: 3–7 items, 1 line each
- Business rules: 3–10 bullet points
- Total document: < 60 lines
