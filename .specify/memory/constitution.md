<!-- Sync Impact Report
Version change: 1.1.0 → 2.0.0 (MAJOR - the language rule was redefined incompatibly)
Modified (Language): everything in the repository is English, including governance documents,
commit messages and pull request descriptions. Two exceptions: conversation with the team, and
strings shown to the end user.
Modified: the whole file is now written in English, as the rule requires.
Note: the "Project principles" section is still to be filled in with /speckit.constitution.
-->

# Project Constitution

> This file has two parts. **General development rules**, **Workflow** and **Governance** are the
> shared baseline across projects: they are preserved when the file is regenerated with
> `/speckit.constitution`. **Project principles** is the part to fill in, and the part that changes
> from one project to the next.
>
> Every spec, plan and task aligns with both parts.

---

## Project principles

<!-- To be filled in with /speckit.constitution.
     This is where the domain dependent principles go: what comes first, what is never done, which
     legal or product constraints govern the choices. Number them in roman numerals (I, II, III...)
     and write each one as a verifiable rule, not as an intention. -->

---

## General development rules

These hold for every project and do not depend on the domain.

### Tests before code

Development is test driven: the test is written before the code, in the order red, green, refactor.
All code is covered by unit tests to at least 80%. Integration tests are mandatory for API
endpoints, payment flows and the handling of sensitive data.

Pure logic has deterministic tests. I/O (database, network, SDKs) is mocked.

For behaviour preserving refactoring, characterization tests are written before moving or splitting
the code, and are verified green before and after (golden master). No split, no extraction without
a green safety net.

### Tests run on our own runners

CI runs on **self-hosted runners registered on the VPS**, not on the runners hosted by the CI
provider. Jobs are assigned by runner label (convention in use: `[self-hosted, ci]`). The reason is
a stable environment, with system dependencies and services already present, and independence from
the timing and limits of shared runners.

### File size

No source file goes over **500 lines**, with 300 to 500 as the target. A file that grows past the
threshold is split into modules cohesive by responsibility, with a barrel of re-exports where that
preserves the public API. The limit applies to the extracted modules too. Legacy code is brought
under the threshold when it is touched, with no bulk conversions.

**The limit applies to declarative files too**, database schemas included, not only to code. A
single schema file is where this rule is most often quietly broken, and where breaking it hurts
most: everyone touches it, so every change conflicts with every other. Schemas are split per module
from the first table, using whatever mechanism the chosen ORM provides.

### Simplicity and iteration

Development is incremental. Nothing is built until it is actually needed: features meant for a
later phase are not anticipated, and no abstraction is added until there are at least two concrete
cases requiring it. A stated requirement counts as a concrete case, a prediction does not.

Every pull request solves a concrete problem. A simple, tested solution is preferred to an elaborate
architecture.

### Narrow but complete scope

Better a narrower scope that is complete than a wide one with operational holes. When the system
replaces an existing process, it covers 100% of what that process needs to actually work.
Exclusions are approved, not discovered in production.

### No placeholders

Never placeholder features, fake integrations, unimplemented options or partial features presented
as complete. Every element visible in the interface works. If something is not ready, it does not
appear in the menu.

### The navigation is designed once, not accumulated

A confusing admin panel is not a matter of taste, it is a structural outcome. Each feature adds its
own menu entry wherever it seems to fit, nobody owns the whole, and after thirty features the menu
is thirty uncoordinated decisions. What that produces is always the same: the same function
reachable from two places, two pages that do almost the same thing because the second one did not
know about the first, and functions buried three levels deep where nobody finds them again.

The rules that prevent it:

- **One navigation map, and it is the source of truth.** A single versioned file declares every
  section, every entry, the route it points at and what it is for. The interface builds its menu
  from that file, never from routes discovered around the codebase. A screen that is not in the map
  does not exist; an entry in the map with no screen is a bug. **An automated test enumerates the
  application's routes and fails when the two disagree.** This is what makes a duplicate visible in
  a diff instead of six months later.
- **Two levels, not three.** Every function is reachable in at most two steps from the home: a
  section, then a page. A third level requires a recorded exception with its reason. Depth is the
  mechanism by which a working feature becomes an invisible one.
- **One home per capability.** A capability lives in exactly one place. Shortcuts from elsewhere are
  welcome, but they link to that place and never reimplement it. Two pages that do almost the same
  thing are one page that someone has not merged yet.
- **Sections are named after what people do, not after how the code is organised.** A menu that
  mirrors the module structure is a menu designed for the people who wrote the code. The words come
  from the vocabulary the users already use for their own work.
- **A spec that adds a screen says where it goes.** Its position in the map, and what moves, merges
  or disappears as a result. The question is answered while specifying, not discovered while
  implementing. The default answer to "this needs a page" is "inside an existing page", and a new
  entry has to earn itself.
- **The quality audit checks it.** No route outside the map, nothing deeper than allowed, no two
  entries with the same purpose, and one person who has never seen the application finds five named
  functions without being told where they are. That last check is the only one that needs a human
  and it takes five minutes.

### The tracking plan is designed once, not accumulated

The same structural failure as the navigation, in a different place. Each feature emits the events
it happens to need, named however the person writing it named them, and nobody owns the whole. After
thirty features the same action is tracked under three names, a property called `user_id` means one
thing in one event and something else in another, and the first time somebody asks a question of the
data it turns out the data cannot answer it. Product analytics tools and lifecycle messaging tools
sit downstream of this: they faithfully report whatever mess reaches them.

- **One tracking plan, and it is the source of truth.** A single versioned file declares every
  event: its name, when it fires, the properties it carries and what each property means. The code
  emits events through that declaration, not through free-form strings.
- **An event is declared before it is emitted.** **An automated test fails when the code emits an
  event that is not in the plan, or when a declared event carries properties the plan does not
  list.** This is what keeps the plan honest; without it, it becomes documentation of what somebody
  intended a year ago.
- **Naming is a convention, not a decision per event.** One shape for every name, one vocabulary
  drawn from the domain the users work in, and the same property means the same thing everywhere.
- **A spec that adds a user-visible behaviour says what it makes measurable**, or says explicitly
  that it makes nothing measurable, which is also an answer. Deciding while specifying costs
  nothing; instrumenting afterwards means shipping and waiting another month for data.
- **Events are removed like code.** An event nobody has queried in a year is deleted from the plan
  and from the code, rather than being kept because deleting it feels risky.

The role that owns this is product analytics, not data engineering: the two are often confused, and
the second one is about pipelines and warehouses while this is about deciding what is worth
recording in the first place.

### New dependencies

No framework, ORM, UI library or dependency is introduced without a concrete need that cannot be
met with what is already there. Every new dependency is justified in the plan and approved. Absent
a real need, the answer is to reuse.

### A version number is never written from memory

Whoever writes a dependency version looks it up in the registry at that moment. This applies to
people and applies with particular force to models: a model writing `^5.8.0` is recalling a number
that was common in its training data, with no way of knowing that two major versions have shipped
since. The number looks deliberate and is not.

The evidence this rule comes from: two unrelated projects in this organisation, written months apart
with no shared scaffold, both declare Prisma `^5.8.0`, a version from January 2024. One of them was
started three days after Prisma 7.0 was released. The same codebase declares Express `^4.18.2`, from
April 2022, while Express 5 has been out for a year. Nobody chose those numbers; they were
remembered.

- On the first commit, and whenever a dependency is added, the current version is **checked against
  the registry**, not recalled. `npm view <package> version` costs a second.
- The version written down is the **installed** one, from the lockfile, not the range. `^5.8.0` is a
  floor and says nothing about what runs.
- **An automated check enforces it**, because a version number is machine checkable and therefore
  gets machine checked: CI fails when a direct dependency is more than one major behind what the
  registry currently publishes.
- Staying behind on purpose is a decision like any other, recorded with its reason, and the check is
  told about it rather than being switched off.
- When a dependency ships a capability behind a preview flag that would solve a problem the project
  has, it is evaluated when it ships, not discovered years later.

### External integrations behind adapters

Every integration with an external service lives in a dedicated adapter. Business logic depends on
an abstract interface, not on the concrete provider. No provider specific detail (endpoints,
formats, protocol quirks) leaves its adapter. That keeps the provider replaceable and confines the
blast radius of a change on their side.

### Business logic belongs to the backend

Where a backend exists, it owns the logic and the integrations. Clients consume its APIs and do not
reimplement the same logic. Anything touching secrets, tokens or credentials lives in the backend
and never in the frontend. The OpenAPI specification is updated on every endpoint change, generated
automatically where possible.

The principle is about ownership of the logic, not deployment topology: the backend may be split
into modules or processes, as long as the logic stays in one place.

### One source of truth

No parallel systems and no diverging copies of the same data. What one component writes is
immediately visible to the others, because they read the same source.

### Versioned request collections

Every feature that adds or changes endpoints ships the request collections for manual and
exploratory verification. Convention in use: Bruno, `.bru` files versioned under the API folder,
with the environment variables in a separate folder. They are development tools and are not
deployed.

### Branching

- `main` is production. Every merge into `main` is a release.
- `develop` is the integration branch, and the development environment deploys from it.
- `feature/<name>` starts from `develop` and is merged into `develop`.
- `release/<x.y.z>` is optional, to stabilise before going to `main`.
- `hotfix/<name>` starts from `main` and is merged into both `main` and `develop`.

Rules:

- Never commit directly to `main` or to `develop`.
- Right after the merge into `develop`, the same feature is carried to `main` on an isolated branch
  that starts from `main` and takes the commits of that feature alone (cherry-pick), with a pull
  request against `main`. This is proposed unprompted: the merge into `develop` is half the work,
  not the end of it.
- Never merge `develop` into `main` in bulk. Moving dozens of unrelated commits is not a release,
  and it drags along work that was deliberately held back. One feature is released at a time.
- Merges into `main` are `--no-ff`, with a version tag.

### One worktree per piece of work

Every session that modifies code happens in a dedicated git worktree, never in the main repository.
One feature or one refactor equals one worktree, created from `develop`. The main repository is not
used as a working area while worktrees are active. Merging happens through a pull request, and the
worktree is removed when the work is done.

### Code review

Every pull request needs at least one review before merging.

### CI pipeline

On every pull request: lint, type check, unit tests, integration tests.

### Database migrations

Migrations are forward only: additive, backward compatible and idempotent, applied exactly once,
deterministically, by the same mechanism in CI and at deploy time. **They are applied before the
code that uses them.** Rollback is at the application level (restore the previous commit and
restart), not on the schema. Destructive changes such as drop and rename are performed in backward
compatible steps following the expand and contract pattern, never in the same deploy as the code
that requires them.

### Dangerous and irreversible operations

Dangerous operations ask for confirmation or warn, but never leave the operator unrecoverably
stuck. Irreversible operations are idempotent or deduplicated, so a double execution does not
produce a double effect. Actions against shared environments (deploys, migrations, destructive
actions) are surfaced and confirmed before they run.

### Deploy

Deployment happens only through git: commit, push, pull on the server, rebuild. Never scp, rsync or
direct file copying onto the server. Development and production environments stay separate.

### Secrets

No secret in the code. Locally, environment variables in an unversioned file; in production, a
secret manager. An automated check fails the build if it finds a secret in the repository. Secrets,
tokens and credentials are encrypted at rest and never appear in clear text in interfaces, API
responses or logs.

### Access control and audit trail

Sensitive operations are protected by a role check, not by authentication alone. Sensitive means
destructive operations and those touching permissions, money, configuration or personal data. Every
execution leaves a row in an append only log: who, what, when, on which object, with what outcome.
The log is never modified and never deleted.

### Identity comes from the session

The identity of the caller, and the scope of data they are entitled to, always come from the
session or the token, never from a field in the request body or query. The client says which object
it wants to act on, the server verifies that the object falls inside the caller's scope. This
prevents reaching another party's data by changing an identifier in the request.

### Policies that change from project to project are configuration

What varies from one project or one customer to another is not written in the code and not fixed in
a document: it is set from a control panel and the system reads that value. Typical examples: the
language of generated content, numeric thresholds, taxonomies, which fields are mandatory, which
automations are on, who receives notifications.

Every setting has a sensible default, exactly one place where it lives (never the same setting in
two places), and a stated owner who can change it. When a behaviour starts being requested
differently for a particular case, it becomes a setting, not an `if` branch in the code.

The simplicity rule applies: a setting is created when the variability is real, meaning there are at
least two concrete cases or an explicit request. No control panel is built for imagined differences.

### Feature activation

Features are individually switchable and stay off by default. No interface appears for a feature
that is not active.

### Personal data

If the project handles personal data: a stated purpose, encryption at rest and in transit for
sensitive data, hosting in the European Union, explicit consent, the right to erasure available from
the first version, no sharing with third parties without consent. If it handles special category
data under Article 9 GDPR (health, biometrics and the like), that becomes a project principle with
explicit requirements in every spec that touches it.

### Language

**Everything in this repository is written in English**: code, comments, identifiers, file names,
technical log messages, spec-kit artifacts (`specs/**`, contracts, checklists), documentation,
governance documents, README files, commit messages and pull request descriptions. No Italian in
the repository, and no Italian words dressed up as English.

Two exceptions, and only two:

- **Conversation with the team** happens in whatever language the team speaks.
- **Strings shown to the end user**, which are never hardcoded anyway: they go through the
  localisation system or the per product configuration. Tickets generated on a tracker are the case
  in point: English by default, and bilingual English plus the team's language when some members of
  that team do not read English.

Legacy code and documents in another language are converted when they are touched, like the line
limit: no bulk conversion, but no new non-English content.

### Questions asked in an understandable way

Whoever asks a question carries the burden of making it understandable to whoever must answer. If
the answer comes back confused or off target, the question was badly written: rephrase it, do not
insist.

- **No acronyms and no internal codes.** Never write things like "D4 exposes principle VII and
  blocks S725". Name what you are talking about in full, every time, even if it already appeared
  earlier in the conversation.
- **Start from the concrete situation**: what happens today, in which part of the system, with which
  data. Then the choice to make. Then what actually changes between one option and another, with a
  real example taken from the project.
- **Explain technical terms** the first time they appear, in one line.
- **Do not assume** the reader remembers an earlier discussion or holds the same context as the
  writer.
- **Few questions at a time.** Long lists only when the questions are genuinely independent; if the
  answer to one changes the others, ask one thing at a time.
- **Check before asking.** If the answer can be found by looking at the code, the backlog or the
  documentation, look instead of asking.
- **Only ask when the answer changes something.** If the options are equivalent in practice, or if
  one is clearly reasonable and the other is not, choose, state the choice in one line, and move on.
  Asking something you could have decided yourself moves work from the person who knows how to do it
  to the person who should not have to.

### Do not get stuck on what does not block

A missing piece of data, an access, a file, a decision: carry on with everything else and come back
to it later. You stop only when, without that thing, the work that follows would have to be redone
from scratch, and in that case you say it once and move to the next piece of work.

- A missing thing is recorded where it matters, together with the assumption taken in its place, and
  the work continues. It is not brought up again in every message.
- A gap does not become a theme: no repeated lists of what is missing, no reminders about things
  already flagged, no passive waiting.
- Priority is not decided by whoever is working on the piece: if something looks important but
  nobody asked for it now, note it and move on.
- Marginal issues are noted and left alone. A detail corrected at the wrong moment costs more than
  it is worth.

### Plain register

Write plainly and factually, in documents and in replies. Constraints and dependencies are stated as
facts.

Blockers exist and are named when they are there. What is not needed is the solemn register built to
create urgency: "blocks all progress", "non-negotiable", "immediate action required", "determines
success or failure", bold used to raise the voice. If everything sounds critical, nothing stands out
as actually critical. A serious thing is said once, plainly, and then you move on.

---

## Workflow

1. Medium and large features are specified with spec-kit before implementation, following the flow
   `specify → clarify → plan → tasks → analyze → implement`. Focused fixes and minor changes proceed
   directly. Inside the flow, `implement` is the only step that creates or modifies production code.
2. **A spec is written just before its feature is built.** A spec written far ahead of its
   implementation is a **sketch**, and it says so at the top, with the date it was written and an
   instruction to refresh it before planning. What holds intent durably is the backlog entry; a spec
   becomes authoritative when its feature is next.

   Sketching several features at once is worth doing when it reveals boundaries, dependencies or
   gaps that were invisible one feature at a time. What it does not do is produce artifacts that
   stay true: a sketch stops matching reality the moment anything is learned, and the danger is not
   that it is wrong, it is that it looks finished.

   Refreshing a sketch belongs to `clarify`: everything learned since it was written is exactly what
   there is to clarify. A spec that has been planned and is being implemented is kept current in the
   same pull request as the code, like every other document that diverges.
3. Significant design decisions (data model, choice of an external adapter, deviations from the
   principles) are documented in the plan and checked against this constitution (Constitution
   Check).
4. **Quality audit before closing the cycle.** At the end of every spec-kit cycle, before declaring
   it closed, an audit runs over the code produced. It checks at least:
   - file size under the threshold;
   - test coverage at 80% or above, with the suite green;
   - no placeholders and no partial features presented as complete;
   - duplicated logic, and logic that leaked into a client instead of the backend;
   - external provider details that escaped their adapter;
   - the navigation: no route outside the map, nothing too deep, no two entries with the same
     purpose, and five named functions found by someone who has not seen the application;
   - secrets, language of the code, adherence to the constitution's principles.

   Whatever the audit finds is refactored before closing. The cycle is not declared closed while
   findings remain open. If something cannot be resolved within the cycle, the exception is
   justified and approved, and becomes a recorded task with a deadline.
5. Database schema changes are applied before the code that uses them.
6. Deployment to the development environment happens after every completed feature, for validation,
   before promotion to production.

## Governance

- The constitution takes precedence over other development practices.
- Changes require a documented rationale, approval, and a migration plan if they break something
  existing.
- Every pull request and every review checks compliance with the principles. Violations are
  justified in the plan and approved.
- Added complexity is justified with a concrete use case.
- Divergences between documents are flagged, not silently absorbed.
- Versioning: patch for a clarification, minor for an added principle or section, major for a
  principle removed or redefined incompatibly. A Sync Impact Report at the top of the file records
  the version change and what changed.

**Version**: 2.0.0 | **Ratified**: [DATE] | **Last amended**: [DATE]
