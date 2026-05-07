---
name: rules-engineering
description: >
  Transform workspace context into structured steering documents that guide AI tools.
  Creates 4 core rule files (product.md, tech.md, structure.md, patterns.md) in
  <workspace-root>/docs/rules/ and tool-specific memory-bank.md files for Kiro,
  GitHub Copilot, Google Antigravity, and Claude. Use when: user asks to create
  steering documents, AI rules, project rules, memory bank, or context files for AI tools.
  Trigger phrases: "create steering documents", "create rules", "create AI rules",
  "create memory bank", "rules engineering", "doc rules", "set up AI context".
  Also use when someone wants to teach an AI tool about their project before writing code.
license: MIT
compatibility: Claude Code, Cursor, VS Code, Windsurf, Kiro, Github Copilot, Antigravity
metadata:
  category: methodology
  complexity: intermediate
  author: Jenny Santos
  version: "1.0.0"
---

# Rules Engineering

Transform workspace context into steering documents that tell AI tools what your project is, what it uses, how it is organized, and how it should be built. Output is 4 rule files shared across all tools plus tool-specific memory-bank entry points.

## When to Use This Skill

- Setting up a new project for AI-assisted development
- Onboarding an AI tool to an existing codebase
- Standardizing AI behavior across multiple tools (Kiro, Copilot, Claude, Antigravity)
- Updating AI context after a major tech or architecture change
- Ensuring generated code matches the project's patterns and conventions

---

## Output Structure

All 4 rule files land in `<workspace-root>/docs/rules/` — shared by every tool:

| File | Purpose |
|------|---------|
| `docs/rules/product.md` | Product purpose, target users, key features, business goals |
| `docs/rules/tech.md` | Frameworks, libraries, tools, technical constraints |
| `docs/rules/structure.md` | File organization, naming conventions, module boundaries |
| `docs/rules/patterns.md` | Architecture patterns, best practices, coding conventions |

Each tool then gets its own `memory-bank.md` that references those shared files:

| Tool | Memory Bank Location |
|------|---------------------|
| Kiro | `.kiro/steering/memory-bank.md` |
| GitHub Copilot | `.github/instructions/memory-bank.md` |
| Google Antigravity | `.agents/rules/memory-bank.md` |
| Claude | `.claude/rules/memory-bank.md` |

---

## 6-Step Workflow

### Step 1: Analyze the Workspace

Explore the project before writing anything. Read:
- `package.json`, `pyproject.toml`, `Cargo.toml`, or equivalent
- Entry points (`main.*`, `index.*`, `app.*`, `server.*`)
- Directory tree (top 2 levels)
- Existing docs (`README.md`, `CLAUDE.md`, `CONTRIBUTING.md`)
- Config files (`.eslintrc`, `tsconfig.json`, `Dockerfile`, etc.)

Goal: understand the product domain, tech choices, folder layout, and recurring patterns before drafting anything.

→ See [steering_concepts_guide.md](references/steering_concepts_guide.md) for what steering documents are and how they influence AI tools.

### Step 2: Draft Product Overview (`product.md`)

Write for the AI, not for users. Answer:
- What problem does this product solve?
- Who are the primary users?
- What are the 3–5 most important features?
- What business or domain rules constrain decisions?
- What outcomes define success?

Keep it concise — 1–2 paragraphs per section. Avoid marketing language. Use concrete verbs.

→ See [product_guide.md](references/product_guide.md) for section structure, anti-patterns, and worked examples.

### Step 3: Draft Technology Stack (`tech.md`)

Document every deliberate technology choice so the AI defaults to your stack:
- Runtime / language and version
- Primary framework(s)
- Key libraries (state management, ORM, HTTP client, testing, etc.)
- Build tools, bundlers, linters, formatters
- Infrastructure and deployment (cloud provider, containers, CI/CD)
- Hard constraints ("never use X", "always use Y for Z")

→ See [tech_guide.md](references/tech_guide.md) for category breakdown, constraint format, and examples.

### Step 4: Draft Project Structure (`structure.md`)

Map how the codebase is organized so generated code lands in the right place:
- Top-level directory tree with one-line purpose per folder
- Module / layer boundaries (what can import what)
- File naming conventions (case, suffix, co-location rules)
- Where tests, assets, configs, and generated files live

→ See [structure_guide.md](references/structure_guide.md) for tree format, naming rules, boundary notation, and examples.

### Step 5: Draft Project Patterns (`patterns.md`)

Capture recurring implementation decisions the AI should follow or avoid:
- Architectural style (MVC, hexagonal, feature-sliced, etc.)
- Data-fetching patterns (REST, GraphQL, SDK calls, caching strategy)
- Error handling conventions
- State management approach
- Security patterns (auth, input validation, secrets)
- Testing strategy (unit / integration / e2e split, mocking policy)
- Anti-patterns explicitly banned in this codebase

→ See [patterns_guide.md](references/patterns_guide.md) for pattern format, do/don't tables, and worked examples.

### Step 6: Identify Active Tool(s) and Generate Memory Banks

**Before generating any file**, identify which AI tool is running this skill.

#### Auto-detection (check in order)

1. **Kiro** — `.kiro/` directory exists at workspace root, or user invoked from Kiro
2. **GitHub Copilot** — `.github/` directory exists with `copilot-instructions.md`, or VS Code + Copilot extension context
3. **Google Antigravity** — `.agents/` directory exists, or user invoked from Antigravity
4. **Claude** — `.claude/` directory exists, or `CLAUDE.md` present at workspace root, or running inside Claude Code

If auto-detection is ambiguous or inconclusive, **ask the user**:

> "Which AI tool are you using in this workspace?
> Options: Kiro / GitHub Copilot / Google Antigravity / Claude / Multiple (list them)"

#### Generate only for confirmed tools

Generate `memory-bank.md` **only** for tools the user confirmed. Do not create directories or files for unused tools.

| Tool | Memory Bank Location | Reference syntax |
|------|---------------------|-----------------|
| Kiro | `.kiro/steering/memory-bank.md` | `#[[file:path]]` |
| GitHub Copilot | `.github/instructions/memory-bank.md` | `[label](path)` |
| Google Antigravity | `.agents/rules/memory-bank.md` | `[label](path)` |
| Claude | `.claude/rules/memory-bank.md` | `[label](path)` |

Each `memory-bank.md` is a lightweight reference — links to the 4 shared docs, never a copy of them.

→ See [tool_integration_guide.md](references/tool_integration_guide.md) for exact front-matter, reference syntax, and full template per tool.

---

## Quick Example

```markdown
# docs/rules/product.md

## Product Overview
Task management SaaS for software teams. Users create projects, assign tasks,
track status, and collaborate via comments. Core value: eliminate status meetings
by making work visible in real time.

## Target Users
- Software engineers managing personal workload
- Tech leads coordinating 5–15 person teams

## Key Features
1. Kanban board with drag-and-drop
2. Task comments with @mentions and notifications
3. GitHub PR auto-link when branch name matches task ID
4. CSV export for sprint retrospectives

## Business Rules
- Free tier: max 3 projects, 10 tasks each
- Paid tier: unlimited; billing via Stripe
- Data must not cross region boundaries (EU/US isolation)
```

```markdown
# docs/rules/tech.md

## Runtime
- Node.js 20 LTS, TypeScript 5.x (strict mode)

## Frontend
- React 18, Vite, TailwindCSS v3
- Zustand for global state; React Query for server state

## Backend
- Express 5, Zod for validation, Prisma ORM
- PostgreSQL 16 (primary), Redis 7 (cache/pub-sub)

## Testing
- Vitest + Testing Library (unit/integration)
- Playwright (e2e, critical flows only)

## Constraints
- Never use `any` in TypeScript — use `unknown` + type guard
- Use Prisma migrations only — no raw SQL schema changes
```

---

## Checklist

Before delivering, confirm:

- [ ] All 4 rule files exist in `docs/rules/`
- [ ] `product.md` answers: what, who, why — no marketing fluff
- [ ] `tech.md` lists every major dependency + hard constraints
- [ ] `structure.md` includes annotated directory tree + naming rules
- [ ] `patterns.md` covers architecture, error handling, testing, anti-patterns
- [ ] Memory-bank files generated for all tools in use
- [ ] Memory-bank files reference `docs/rules/` — not copy content
- [ ] No secrets or environment-specific values in any rule file

---

## Output

Use the blank templates in [assets/](assets/) as starting points:
- [assets/product-template.md](assets/product-template.md)
- [assets/tech-template.md](assets/tech-template.md)
- [assets/structure-template.md](assets/structure-template.md)
- [assets/patterns-template.md](assets/patterns-template.md)

For complete guidance on each document and tool integration, see the [references/](references/) folder.
