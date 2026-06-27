---
description: Custom spec-kit workflow — activate whenever discussing feature implementation or spec-kit commands
activation: when discussing feature implementation or spec-kit commands
---

# Spec-Kit Workflow

## The 7 commands in order

| # | Command | Purpose |
|---|---------|---------|
| 1 | `/speckit.constitution` | Define project principles (done once per project) |
| 2 | `/speckit.specify` | Create feature specification (user stories, requirements, success criteria) |
| 3 | `/speckit.clarify` | Resolve ambiguities before technical planning |
| 4 | `/speckit.plan` | Technical plan (stack, file structure, architectural decisions) |
| 5 | `/speckit.tasks` | Generate executable task list with dependencies and phases |
| 6 | `/speckit.analyze` | Verify consistency: spec ↔ plan ↔ tasks |
| 7 | `/speckit.implement` | **THE ONLY command that generates/modifies production code** |

## Core Rule

`/speckit.implement` is the **only** command authorized to create or modify
files in production source directories (`src/`, `apps/`, `shared/`, `infra/`, `.github/`).

If the user asks "implement X" without existing spec/plan/tasks:
> "To implement X I need the spec first. Shall I launch `/speckit.specify`?"

## specs/ folder structure

Each feature has a folder `specs/NNN-slug-feature/` containing:

```
specs/NNN-feature-slug/
├── spec.md          User stories, functional requirements, success criteria
├── plan.md          Technical plan, stack, file structure, arch decisions
├── tasks.md         Task list with phases, dependencies, [P] markers for parallel
├── data-model.md    Entities, relations, DB schema, migration SQL  (optional)
├── research.md      Technical decisions and rationale (R1, R2, …)  (optional)
└── contracts/
    └── api-name.md  API contracts (endpoints, request/response)    (optional)
```

## Task list — format and conventions

```markdown
## Phase 1: Setup
- [ ] T001 Verify remote DB connection
- [ ] T002 Verify dependency X in package.json

## Phase 2: Foundational
- [x] T003 [P] Parallel task already completed
- [ ] T004 Sequential task
```

- `[P]` = task executable in parallel with others in the same phase
- `[x]` = completed task
- `[ ]` = pending task
- Phases in order: Setup → Foundational → Feature core → Frontend → Polish → Deploy

## Prerequisites checklist before `/speckit.implement`

Before running implement, verify that these exist:
- `specs/NNN-slug/spec.md` ✅
- `specs/NNN-slug/plan.md` ✅
- `specs/NNN-slug/tasks.md` ✅ (with at least one `[ ]` task)

If even one is missing → ask to complete the workflow first.

## Standard response to "implement X"

```
Before proceeding I verify that spec/plan/tasks exist for "X".

[if they exist]
Found specs/NNN-X/ with plan and tasks. Proceed with /speckit.implement?

[if they don't exist]
No spec found for "X". Shall I launch /speckit.specify to create it?
I'll need: feature description, user goal, constraints.
```

## Safety Rules

- ❌ Never generate code without approved spec+plan+tasks
- ❌ Never skip workflow phases even if it "seems simple"
- ❌ Never modify already-approved spec/plan without a new clarify cycle
- ✅ Reading and explaining any existing spec is always allowed
- ✅ Suggesting improvements to specs is always allowed
- ✅ Creating/modifying files in `specs/` and `.specify/` is always allowed
