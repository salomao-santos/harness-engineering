# Steering Documents: Concepts & Principles

## What Are Steering Documents?

Steering documents are context files that AI tools read before responding to requests. They describe the **what**, **why**, **how**, and **where** of a project so the AI produces code and suggestions that fit the codebase — not generic examples.

Without steering documents, an AI tool:
- Suggests libraries you don't use
- Generates files in the wrong folder
- Ignores your naming conventions
- Introduces patterns you've already banned

With steering documents, the AI behaves like a developer who has already read the README, explored the repo, and absorbed the team's conventions.

## The 4 Core Documents

| Document | Answers | Used by AI when |
|----------|---------|-----------------|
| `product.md` | What is this? Who uses it? What rules apply? | Making product-domain decisions |
| `tech.md` | What stack? What's forbidden? | Suggesting libraries, writing imports |
| `structure.md` | Where does code live? How is it named? | Creating or moving files |
| `patterns.md` | How is code written here? What is banned? | Implementing features, handling errors |

## How They Flow Into AI Tools

Each tool has its own mechanism for loading context. The `memory-bank.md` is the tool-specific entry point that references the shared `docs/rules/` files. This way:

- The 4 source-of-truth documents live in one place (`docs/rules/`)
- Each tool loads them via its own native reference mechanism
- Updating one file propagates to all tools automatically

## Inclusion Strategies (Kiro-style Front-matter)

For tools that support it (Kiro), you can control when a document is loaded:

```yaml
---
inclusion: always          # loaded in every interaction
---

---
inclusion: fileMatch
fileMatchPattern: '*.tsx'  # loaded when a .tsx file is in context
---

---
inclusion: manual          # loaded only when explicitly referenced
---
```

For tools that don't support front-matter (Copilot, Claude, Antigravity), all referenced documents are loaded automatically via the memory-bank file.

## Writing Principles

**Be specific, not aspirational.** Document what IS true, not what you hope will be true. A steering document that says "we use TypeScript strict mode" when the codebase has 200 `any` usages confuses the AI.

**Use concrete values.** Instead of "use modern React patterns", write "use function components with hooks — no class components". Instead of "follow security best practices", write "use parameterized queries — never string-concatenate SQL".

**Short over long.** AI tools have context budgets. A 200-line steering document that covers everything is better than a 2000-line document that the tool truncates.

**Separate concerns.** Keep each of the 4 documents focused on its domain. Don't mix tech constraints into product.md or put naming conventions in patterns.md.

## When to Update Steering Documents

Update steering documents when:
- You add or remove a major dependency
- The folder structure changes
- You adopt or ban a new pattern
- The product pivots or adds a new domain
- You onboard a new AI tool

Stale steering documents are worse than no steering documents — the AI confidently generates code based on outdated context.
