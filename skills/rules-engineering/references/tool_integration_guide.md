# Tool Integration Guide (`memory-bank.md` per tool)

## Overview

The 4 rule files (`product.md`, `tech.md`, `structure.md`, `patterns.md`) always live in:

```
<workspace-root>/docs/rules/
```

Each AI tool needs its own `memory-bank.md` — a lightweight entry point that references the shared docs. The memory-bank is **never a copy** — only links/references. This way updating one doc propagates to all tools.

---

## Kiro

**Location:** `<workspace-root>/.kiro/steering/memory-bank.md`

Kiro uses front-matter to control inclusion. Use `inclusion: always` so the memory-bank loads in every session.

```markdown
---
inclusion: always
---

# Project Memory Bank

This file is the entry point for AI steering in this workspace.
Full rule documents live in `docs/rules/`. Load them as needed.

## Rule Documents

- **Product Overview** — What this product does, who uses it, business constraints
  #[[file:../../docs/rules/product.md]]

- **Technology Stack** — Frameworks, libraries, versions, hard constraints
  #[[file:../../docs/rules/tech.md]]

- **Project Structure** — Directory layout, naming conventions, module boundaries
  #[[file:../../docs/rules/structure.md]]

- **Project Patterns** — Architecture style, coding patterns, anti-patterns
  #[[file:../../docs/rules/patterns.md]]
```

> `#[[file:path]]` is Kiro's native file-reference syntax. Kiro reads and injects the referenced file's content automatically.

---

## GitHub Copilot

**Location:** `<workspace-root>/.github/instructions/memory-bank.md`

Copilot reads `.github/instructions/*.md` files as custom instructions. Use standard markdown — no front-matter needed.

```markdown
# Project Memory Bank

Context files for this workspace. Read all referenced documents before
suggesting code.

## Rule Documents

- [Product Overview](../../docs/rules/product.md) — What this product does, target users, business constraints
- [Technology Stack](../../docs/rules/tech.md) — Frameworks, libraries, versions, hard constraints
- [Project Structure](../../docs/rules/structure.md) — Directory layout, naming conventions, module boundaries
- [Project Patterns](../../docs/rules/patterns.md) — Architecture style, coding patterns, anti-patterns
```

> Copilot resolves markdown links relative to the workspace root. Paths are relative to the memory-bank file location.

---

## Google Antigravity

**Location:** `<workspace-root>/.agents/rules/memory-bank.md`

Antigravity loads files from `.agents/rules/` as workspace context rules.

```markdown
# Project Memory Bank

Workspace rule documents for AI context. Full content in `docs/rules/`.

## Rule Documents

- [Product Overview](../../docs/rules/product.md)
- [Technology Stack](../../docs/rules/tech.md)
- [Project Structure](../../docs/rules/structure.md)
- [Project Patterns](../../docs/rules/patterns.md)
```

---

## Claude (Claude Code / claude.ai)

**Location:** `<workspace-root>/.claude/rules/memory-bank.md`

Claude Code reads `.claude/` directory files. Reference with standard markdown links.

```markdown
# Project Memory Bank

Workspace context for Claude. Load referenced documents to understand
this project's product, stack, structure, and patterns.

## Rule Documents

- [Product Overview](../../docs/rules/product.md) — Product purpose, target users, business rules
- [Technology Stack](../../docs/rules/tech.md) — Stack choices, library versions, hard constraints
- [Project Structure](../../docs/rules/structure.md) — File layout, naming conventions, import boundaries
- [Project Patterns](../../docs/rules/patterns.md) — Architecture patterns, conventions, anti-patterns
```

> To make Claude auto-load these on every session, also add a reference in `CLAUDE.md`:
> ```markdown
> See [.claude/rules/memory-bank.md](.claude/rules/memory-bank.md) for project rules.
> ```

---

## Summary Table

| Tool | Memory Bank Path | Reference Syntax |
|------|-----------------|-----------------|
| Kiro | `.kiro/steering/memory-bank.md` | `#[[file:path]]` |
| GitHub Copilot | `.github/instructions/memory-bank.md` | `[label](path)` markdown link |
| Google Antigravity | `.agents/rules/memory-bank.md` | `[label](path)` markdown link |
| Claude | `.claude/rules/memory-bank.md` | `[label](path)` markdown link |

All 4 rule files always at: `docs/rules/product.md`, `docs/rules/tech.md`, `docs/rules/structure.md`, `docs/rules/patterns.md`

---

## Identifying Which Tools to Generate

### Auto-Detection (try first)

Check the workspace for tool-specific markers before asking the user:

| Signal | Likely tool |
|--------|------------|
| `.kiro/` directory exists | Kiro |
| `.github/copilot-instructions.md` exists | GitHub Copilot |
| `.github/instructions/` directory exists | GitHub Copilot |
| `.agents/` directory exists | Google Antigravity |
| `.claude/` directory exists OR `CLAUDE.md` at root | Claude |
| User invoked the skill from within the tool's IDE | That tool |

Multiple signals can be true — generate for all confirmed tools.

### When Auto-Detection Is Ambiguous

Ask the user once:

> "Which AI tool are you using in this workspace?
> Options: Kiro / GitHub Copilot / Google Antigravity / Claude / Multiple (list them)"

### Rule: Generate Only for Confirmed Tools

Do not create directories or files for tools not in use. Each tool directory created is a maintenance obligation. If the user says "just Copilot", generate only `.github/instructions/memory-bank.md` — nothing else.

---

## Directory Creation Note

Create parent directories if they don't exist before writing the memory-bank file:

| Tool | Directories to create |
|------|----------------------|
| Kiro | `.kiro/steering/` |
| GitHub Copilot | `.github/instructions/` |
| Google Antigravity | `.agents/rules/` |
| Claude | `.claude/rules/` |

Always also create `docs/rules/` for the shared rule files.
