# Project Development Guidelines

## CORE RULE: SPEC-KIT FOR ANYTHING NON-TRIVIAL

Medium and large features MUST go through the spec-kit workflow before implementation.
Small, focused fixes and minor changes may proceed directly.

Inside the workflow, `/speckit.implement` is the only step that creates or modifies
production code.

### Use spec-kit when the change:
- adds or reshapes a feature, an endpoint, a data model, or a screen
- touches more than a couple of files, or crosses a module boundary
- changes behaviour users or other services depend on

### Go direct when the change is:
- a bug fix with a known cause and a contained blast radius
- a copy change, a config value, a dependency bump, a typo
- anything you can describe in one sentence and cover with one test

### Always FORBIDDEN without an explicit user request:
- Running `npm install`, package generators, or any command that produces code
- Deploying to any server
- Committing directly to `main` or `develop`

### Always ALLOWED:
- Reading any existing file for analysis
- Working inside `specs/` and `.specify/`
- Explaining specs and proposing improvements to them

### The workflow:
1. `/speckit.constitution` — define project principles (once per project)
2. `/speckit.specify` — create feature specification
3. `/speckit.clarify` — resolve ambiguities (optional but recommended)
4. `/speckit.plan` — create technical plan
5. `/speckit.tasks` — generate task list
6. `/speckit.analyze` — verify consistency (optional)
7. `/speckit.implement` — THE ONLY command that generates/modifies production code
8. **Quality audit** — before declaring the cycle closed

If the user asks to implement a feature, respond: "Launching /speckit.implement to proceed with the implementation."

**EXCEPTION**: The user can explicitly say "write the code directly" or "do it without spec-kit" to bypass the workflow for a change that would otherwise need it.

### Step 8 — quality audit before closing

A cycle is not closed when `/speckit.implement` finishes. Audit the code that was produced and
check at least:

- no file over 500 lines
- test coverage at 80% or above, suite green
- no placeholders, no partial features presented as complete
- no duplicated logic, no business logic that leaked into a client
- no external-provider details outside their adapter
- no secrets, code and technical docs in English, principles respected

Whatever the audit finds gets refactored before the cycle is declared closed. If something
cannot be fixed within the cycle, the exception is justified, approved, and recorded as a task
with a deadline.

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

Code and technical documentation are in **English**: identifiers, comments, file names, log
messages, spec-kit artifacts (`specs/**`, contracts, checklists) and READMEs. Team communication
and governance documents follow the team's own language.

Everything else lives in `.specify/memory/constitution.md`, under "Regole generali di sviluppo":
file size, TDD, CI runners, dependencies, adapters, migrations, secrets, branching, worktrees.
Read it before writing code.

<!-- Add project-specific style notes here -->
