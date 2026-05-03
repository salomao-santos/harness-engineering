# Quality Checklist Guide

Run this checklist before delivering `tasks.md` to the user. Every item must pass.

---

## Plan Quality Checklist

### Coverage
- [ ] Every component in `design.md` has at least one corresponding task
- [ ] Every requirement in `requirements.md` is referenced in at least one `_Requirements:` line
- [ ] All `FOR ALL` properties from requirements have a corresponding PBT task

### Traceability
- [ ] Every sub-task ends with `_Requirements: N.N_` (or `_Requirements: All_` for test/doc tasks)
- [ ] No task references code that does not yet exist in a prior task
- [ ] Requirement numbers match exactly those in `requirements.md`

### Detail Level
- [ ] Each sub-task has enough detail for implementation without ambiguity
- [ ] File paths use full paths (e.g., `server/db/taskRepository.ts`, not just `taskRepository.ts`)
- [ ] Concrete values are present: hex colors, HTTP codes, SQL constraints, method names, UI text
- [ ] Component names, prop names, and interface names match `design.md` exactly

### Structure
- [ ] Checkpoints are placed between logical phases (backend, end-to-end, final)
- [ ] Optional tasks are marked with `*` after the closing bracket
- [ ] Numbering is sequential and gap-free (1, 1.1, 1.2, 2, 2.1 …)
- [ ] Task order respects dependencies — no forward references to code not yet created

### Scope
- [ ] No tasks invent features not present in `requirements.md` or `design.md`
- [ ] Tasks contain only implementation activities (no planning, discovery, or research tasks)
- [ ] Sub-task scope is appropriate: 2–4 hours for human developers, smaller for AI agents

---

## Task Planning Completeness

### Task Categories
- [ ] **Foundation Tasks**: Setup and infrastructure tasks are included (project init, shared types, env config)
- [ ] **Core Logic Tasks**: Business logic implementation is covered (repositories, services, domain rules)
- [ ] **Integration Tasks**: System integration work is planned (API routes, API client, end-to-end wiring)
- [ ] **Testing Tasks**: Comprehensive testing tasks are included (unit, integration, PBT)
- [ ] **Documentation Tasks**: Documentation updates are planned where needed

### Development Strategy
- [ ] **Test-Driven Approach**: TDD/BDD strategy is defined where appropriate
- [ ] **Code Quality Standards**: Quality expectations are established
- [ ] **Review Process**: Code review procedures are planned
- [ ] **Integration Strategy**: How components will be integrated is clear

### Risk Management
- [ ] **Technical Risks**: Potential technical challenges are identified
- [ ] **Dependency Risks**: External dependency risks (libraries, APIs, services) are considered
- [ ] **Resource Risks**: Team capacity and skill requirements are assessed
- [ ] **Timeline Risks**: Schedule risks and mitigation strategies are planned

### Stakeholder Review
- [ ] **Technical Approval**: Development team has reviewed and approved tasks
- [ ] **Business Alignment**: Tasks align with business priorities and timeline
- [ ] **Resource Confirmation**: Required resources and skills are available
- [ ] **Timeline Validation**: Task timeline is realistic and achievable

---

## Anti-Patterns to Avoid

| Anti-pattern | Why it fails | Fix |
|-------------|-------------|-----|
| "Implement user management" (too abstract) | Developer cannot start without more information | Break into: Create model, Create service, Create endpoints |
| Task references `TaskRepository` before it is created | Unexecutable | Move the `TaskRepository` creation task earlier |
| No `_Requirements:` in sub-tasks | No traceability; can't validate completion | Add requirement references to every sub-task |
| Sub-task: "Make the UI look nice" | Not actionable, not testable | Specify: component, file, CSS properties, values |
| Skipping checkpoints | Problems accumulate and surface only at the end | Add checkpoints after backend and after integration |
| PBT tasks marked mandatory | Blocks MVP unnecessarily | Mark all PBT tasks with `*` |
| File paths without full path | Ambiguity about where to create the file | Use `server/db/taskRepository.ts` not `taskRepository.ts` |
| Requirement numbers that don't exist | Broken traceability | Verify every `_Requirements:` number against `requirements.md` |

---

## Tasks Phase Quick Checklist (for delivery)

```markdown
# Tasks Quick Checklist

## Document Structure
- [ ] Overview: feature name, stack, task count, phase count
- [ ] Tasks with clear objectives and full file paths
- [ ] Requirements traceability on every sub-task
- [ ] Testing tasks (unit and/or PBT) for each component
- [ ] Checkpoints between logical phases
- [ ] Optional tasks marked with *

## Quality Check
- [ ] All design elements are covered by tasks
- [ ] Tasks build incrementally (no forward references)
- [ ] Concrete values present throughout
- [ ] Implementation risks identified
- [ ] Development team / user has reviewed
```

---

## Handoff to Implementation Phase

After `tasks.md` is approved:

1. Execute tasks in strict numerical order — do not skip phases
2. Mark each sub-task as `[-]` (in progress) when started, `[x]` (done) when complete
3. At each Checkpoint: run all tests, start the server, verify behavior, ask the user for feedback
4. If a task reveals a gap (e.g., a design element was not specified), pause and update `design.md` before continuing
5. Mark optional tasks (`*`) only if time permits after all mandatory tasks are complete

---

## Implementation Execution Checklist

### Before starting each task
- [ ] Read task details thoroughly
- [ ] Review referenced requirements
- [ ] Confirm all dependency tasks are marked `[x]`
- [ ] Plan implementation approach

### During implementation
- [ ] Mark task as `[-]` (in progress)
- [ ] Write tests alongside code
- [ ] Test continuously
- [ ] Document only where WHY is non-obvious

### Before marking complete
- [ ] All acceptance criteria from `_Requirements:` are met
- [ ] Tests pass
- [ ] No regressions in existing functionality
- [ ] Mark task as `[x]`
