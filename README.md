# harness-engineering

Software engineering methodology plugin for Claude Code. Covers the full delivery lifecycle: **requirements → design → implementation tasks**.

---

## Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `requirements-engineering` | `/requirements-engineering` | Transform a vague feature idea into a structured requirements doc (EARS format) |
| `design-engineering` | `/design-engineering` | Transform approved requirements into a technical design doc |
| `implementation-tasks-engineering` | `/implementation-tasks-engineering` | Transform approved design into a sequenced implementation plan (`tasks.md`) |

---

## Install as Claude Code Plugin

```bash
claude plugin install https://github.com/salomao-santos/harness-engineering
```

After installing, the slash commands are available in any project:

```
/requirements-engineering <feature description>
/design-engineering <feature or requirements doc>
/implementation-tasks-engineering <design doc>
```

---

## Use Without Installing (Manual)

Copy any `SKILL.md` directly into your project's `skills/` directory or reference it in your `CLAUDE.md`.

```
skills/
├── requirements-engineering/   ← copy from this repo
├── design-engineering/
└── implementation-tasks-engineering/
```

---

## Workflow

The 3 skills form a sequential pipeline:

```
1. /requirements-engineering   →  requirements.md
2. /design-engineering         →  design.md
3. /implementation-tasks-engineering  →  tasks.md
4. Execute tasks.md top to bottom
```

Each phase is gated: do not start design before requirements are approved, and do not generate tasks before design is approved.

---

## Compatibility

Claude Code · Cursor · VS Code · Windsurf · Kiro · GitHub Copilot · Antigravity

---

## License

MIT — [salomao-santos](https://github.com/salomao-santos)
