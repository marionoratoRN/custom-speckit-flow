# Project Name

> Replace this with your project description.

## Getting Started

### 1. Initialize the project constitution

```
/speckit-constitution
```

This defines the principles that govern all technical decisions. Run once per project.

### 2. Fill in product foundation

Edit `.specify/memory/product-foundation.md` with your product vision, target users, and key decisions.

### 3. Start speccing features

```
/speckit-specify <feature description>
```

## What this template installs

The ten spec-kit commands and their scripts, so the workflow below is executable rather than
described. Plus the governance documents this template exists for: a constitution carrying general
development rules, a product foundation, and a spec backlog.

## Spec-Kit Workflow

| Command | Purpose |
|---------|---------|
| `/speckit-constitution` | Define project principles (once) |
| `/speckit-specify` | Create feature specification |
| `/speckit-clarify` | Resolve ambiguities |
| `/speckit-plan` | Technical plan |
| `/speckit-tasks` | Generate task list |
| `/speckit-analyze` | Verify consistency |
| `/speckit-implement` | Generate/modify production code |

**Rules**: `/speckit-implement` is the only command that touches production source files. And the
artifacts under `specs/` are produced by these commands, never written by hand in the shape they
produce: an artifact that looks generated claims a process ran, and nobody rereads a document that
looks finished.

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
