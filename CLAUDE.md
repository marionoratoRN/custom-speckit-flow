# Project Development Guidelines

## CORE RULE: SPEC-KIT WORKFLOW ONLY

**ABSOLUTE PROHIBITION**: Never write, create, modify, or generate implementation code (`.ts`, `.tsx`, `.py`, `.json`, `.yml`, config files, etc.) outside the spec-kit workflow.

### What is FORBIDDEN without explicit user request:
- Creating source code files manually (Write/Edit on files in `src/`, `apps/`, `shared/`, `infra/`, `.github/`)
- Running `npm install`, package generators, or any command that produces code
- Deploying to any server
- Modifying files outside `specs/` and `.specify/`

### What is ALLOWED:
- Running spec-kit commands: `/speckit.constitution`, `/speckit.specify`, `/speckit.plan`, `/speckit.tasks`, `/speckit.implement`, `/speckit.clarify`, `/speckit.analyze`, `/speckit.checklist`
- Reading existing files for analysis
- Modifying files inside `specs/` and `.specify/` as part of the spec-kit workflow

### MANDATORY Workflow:
1. `/speckit.constitution` — define project principles (once per project)
2. `/speckit.specify` — create feature specification
3. `/speckit.clarify` — resolve ambiguities (optional but recommended)
4. `/speckit.plan` — create technical plan
5. `/speckit.tasks` — generate task list
6. `/speckit.analyze` — verify consistency (optional)
7. `/speckit.implement` — THE ONLY command that generates/modifies production code

If the user asks to implement something, respond: "Launching /speckit.implement to proceed with the implementation."

**EXCEPTION**: The user can explicitly say "write the code directly" or "do it without spec-kit" to bypass this rule. Without this explicit instruction, ALWAYS use spec-kit.

## Project Structure

```text
specs/                    # One folder per feature: NNN-slug-name/
  NNN-feature-slug/
    spec.md
    plan.md
    tasks.md
    data-model.md         # optional
    research.md           # optional
    contracts/            # optional: API contracts
.specify/
  memory/
    constitution.md       # project principles
    product-foundation.md # product vision & decisions
  templates/              # spec/plan/tasks/checklist templates
```

## Commands

<!-- Add project-specific test/lint/build commands here -->
<!-- Example: npm test && npm run lint -->

## Active Technologies

<!-- Fill in when project is initialized -->

## Code Style

<!-- Fill in based on project language and conventions -->
