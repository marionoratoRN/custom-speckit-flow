# Project Name

> Replace this with your project description.

## Getting Started

### 1. Initialize the project constitution

```
/speckit.constitution
```

This defines the principles that govern all technical decisions. Run once per project.

### 2. Fill in product foundation

Edit `.specify/memory/product-foundation.md` with your product vision, target users, and key decisions.

### 3. Start speccing features

```
/speckit.specify <feature description>
```

## Spec-Kit Workflow

| Command | Purpose |
|---------|---------|
| `/speckit.constitution` | Define project principles (once) |
| `/speckit.specify` | Create feature specification |
| `/speckit.clarify` | Resolve ambiguities |
| `/speckit.plan` | Technical plan |
| `/speckit.tasks` | Generate task list |
| `/speckit.analyze` | Verify consistency |
| `/speckit.implement` | Generate/modify production code |

**Rule**: `/speckit.implement` is the only command that touches production source files.

## Project Structure

```
specs/                    # One folder per feature
  NNN-feature-slug/
    spec.md
    plan.md
    tasks.md
.specify/
  memory/
    constitution.md
    product-foundation.md
    spec-backlog.md
  templates/
.claude/
  skills/
    speckit-workflow/     # Skill loaded automatically by Claude Code
CLAUDE.md                 # Claude behavior rules (committed to repo)
```
