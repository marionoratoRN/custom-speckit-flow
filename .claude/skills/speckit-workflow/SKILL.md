---
description: Custom spec-kit workflow — activate whenever discussing feature implementation or spec-kit commands
activation: when discussing feature implementation or spec-kit commands
---

# Spec-Kit Workflow

## The 8 steps in order

| # | Command | Purpose |
|---|---------|---------|
| 1 | `/speckit-constitution` | Define project principles (done once per project) |
| 2 | `/speckit-specify` | Create feature specification (user stories, requirements, success criteria) |
| 3 | `/speckit-clarify` | Resolve ambiguities before technical planning |
| 4 | `/speckit-plan` | Technical plan (stack, file structure, architectural decisions) |
| 5 | `/speckit-tasks` | Generate executable task list with dependencies and phases |
| 6 | `/speckit-analyze` | Verify consistency: spec ↔ plan ↔ tasks |
| 7 | `/speckit-implement` | **THE ONLY command that generates/modifies production code** |
| 8 | Quality audit | Verify the produced code before declaring the cycle closed |

## Core Rule

Medium and large features go through this workflow. Small, focused fixes and minor changes
may proceed directly (see CLAUDE.md for where the line sits).

Within the workflow, `/speckit-implement` is the **only** step authorized to create or modify
files in production source directories (`src/`, `apps/`, `shared/`, `infra/`, `.github/`).

If the user asks "implement X" (a feature) without existing spec/plan/tasks:
> "To implement X I need the spec first. Shall I launch `/speckit-specify`?"

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

## Prerequisites checklist before `/speckit-implement`

Before running implement, verify that these exist:
- `specs/NNN-slug/spec.md` ✅
- `specs/NNN-slug/plan.md` ✅
- `specs/NNN-slug/tasks.md` ✅ (with at least one `[ ]` task)

If even one is missing → ask to complete the workflow first.

## Standard response to "implement X"

```
Before proceeding I verify that spec/plan/tasks exist for "X".

[if they exist]
Found specs/NNN-X/ with plan and tasks. Proceed with /speckit-implement?

[if they don't exist]
No spec found for "X". Shall I launch /speckit-specify to create it?
I'll need: feature description, user goal, constraints.
```

## Step 8 — quality audit

`/speckit-implement` finishing is not the end of the cycle. Audit the code that was produced:

- no file over 500 lines
- coverage at 80% or above, suite green
- no placeholders, no partial features presented as complete
- no duplicated logic, no business logic leaked into a client
- no external-provider details outside their adapter
- no secrets; code and technical docs in English; constitution principles respected

Refactor whatever the audit finds **before** declaring the cycle closed. If something cannot be
fixed inside the cycle, justify the exception, get it approved, and record it as a task with a
deadline. Report the audit result explicitly: what was checked, what was found, what was fixed.

## Safety Rules

- ❌ Never generate code for a feature without approved spec+plan+tasks
- ❌ Never skip workflow phases for a feature, even if it "seems simple"
- ❌ Never modify already-approved spec/plan without a new clarify cycle
- ✅ Reading and explaining any existing spec is always allowed
- ✅ Suggesting improvements to specs is always allowed
- ✅ Creating/modifying files in `specs/` and `.specify/` is always allowed
- ❌ Never declare a cycle closed without running the quality audit
