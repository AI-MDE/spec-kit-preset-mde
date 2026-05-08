# mde Preset

This folder contains the mde preset for Spec Kit.

The preset keeps normal Spec Kit artifacts, but changes how they are used in an application repository.

```text
Spec Kit "feature" -> spec / scoped work item
mde capability  -> durable system behavior
```

---

## What It Provides

mde focuses on four concerns:

- streamlined commands
- app repository support
- cross-spec support
- capability-aware project memory

---

## Streamlined Commands

Normal use should stay simple:

```text
specify -> next -> next -> next
```

Command model:

```text
specify   -> create a new spec
clarify   -> refine the current spec
next      -> advance the current spec
status    -> inspect current state
```

`specify` creates a new spec folder.

`clarify` updates the current spec in place.

`next` advances the current spec until a meaningful stop: questions, blocker, readiness, implementation approval, or completion.

`status` is read-only and supports:

```text
status
status project
status capabilities
```

If the user means existing work, switch to the relevant branch and use `clarify`.

---

## Question Files

If the AI has multiple questions, it should create:

```text
questions.md
```

inside the active spec folder.

Each question includes:

```text
question
default answer
rationale
impact if changed
status
```

If the user says `next`, proposed answers in `questions.md` are treated as approved unless the user says they edited or rejected them.

See `QUESTION_POLICY.md`.

---

## App Repository Support

mde is designed for a real app repository, not just isolated spec execution.

Persistent app-wide knowledge lives under:

```text
.specify/memory/app/
```

Typical app memory areas include:

- capabilities
- business requirements
- glossary
- architecture
- data model
- design rules
- policies
- technology stack
- proposed updates

Specs may update code only, or may also update app memory when the change affects broader repository truth.

---

## Cross-Spec Support

mde assumes:

- one capability may be changed by many specs over time
- one spec may affect more than one repository area
- stale specs and stale capabilities need to be detectable

The preset supports that by:

- separating `capability` from `spec`
- using impact analysis before planning
- organizing work around `Change Impact`
- supporting `status project` for stale or incomplete specs
- supporting `status capabilities` for incomplete or stale capabilities

---

## Preset Docs

Authoritative (read by agents):

- [RESOLUTION.md](RESOLUTION.md)
- [QUESTION_POLICY.md](QUESTION_POLICY.md)
- [IMPACT_ANALYSIS.md](IMPACT_ANALYSIS.md)
- [commands/](commands/)
- [templates/](templates/)
- [defaults/](defaults/)

Human reference only (agents must not read from `docs/`):

- [docs/README.md](docs/README.md)
- [docs/rules.md](docs/rules.md) — generated rule index
- [docs/preset-validation.md](docs/preset-validation.md) — post-install validation checklist
- [docs/runtime-context-loading.md](docs/runtime-context-loading.md) — what loads into agent context, when, and why
- [docs/faq.md](docs/faq.md)
- [docs/walkthrough-summary.md](docs/walkthrough-summary.md)
- [docs/terminology.md](docs/terminology.md)
- [docs/capabilities-vs-specs.md](docs/capabilities-vs-specs.md)
- [docs/user-cases.md](docs/user-cases.md)
- [docs/getting-started.md](docs/getting-started.md)
- [docs/architecture.md](docs/architecture.md)

---

## `next` and `status`

`next` and `status` should read the same artifact state.

They infer progress from normal Spec Kit files rather than a separate status file.

Typical signals include:

- `spec.md` exists
- `questions.md` exists and still has unresolved entries
- `plan.md` exists
- `tasks.md` exists
- review or validation artifacts exist
- implementation appears complete or still in progress

The shared statuses are:

```text
DRAFT
READY WITH QUESTIONS
READY FOR PLAN
PLANNED
TASKED
NEEDS REVIEW
IMPLEMENTATION READY
IN PROGRESS
BLOCKED
COMPLETE
```

---

## Normal Artifacts

mde uses normal Spec Kit artifacts as the contract:

```text
spec.md
questions.md
plan.md
data-model.md
contracts/
tasks.md
checklists/
review.md
```

By default, mde does not add:

```text
trace-map files
implementation-contract files
JSON companion files
```

---

## Preset Files

```text
preset.yml
README.md
RESOLUTION.md
QUESTION_POLICY.md
IMPACT_ANALYSIS.md
docs/
  README.md
  rules.md
  preset-validation.md
  runtime-context-loading.md
  architecture.md
  getting-started.md
  faq.md
  walkthrough-summary.md
  terminology.md
  capabilities-vs-specs.md
  user-cases.md
commands/
templates/
defaults/
```

Internal primitive commands still exist for direct use and debugging:

```text
constitution
clarify
checklist
plan
tasks
analyze
implement
```

Normal users should primarily use:

```text
specify
next
status
```

---

## Install

Catalog Install 

```text
specify extension add mde
specify preset add mde

```

Manual copy is the current default for experiments:
```text
specify preset add mde --from https://github.com/AI-MDE/spec-kit-preset-mde/archive/refs/tags/v0.5.1.zip
specify extension add mde --from https://github.com/AI-MDE/spec-kit-mde/archive/refs/tags/v0.5.1.zip

```
Expected result:

```text
.specify/presets/mde/
  preset.yml
  README.md
  RESOLUTION.md
  QUESTION_POLICY.md
  IMPACT_ANALYSIS.md
  docs/
  commands/
  templates/
  defaults/
```

If your Spec Kit version supports preset CLI commands, use the supported preset install path for your version.

---

## Packaging Note

This preset is starting to look like a standalone distributable asset rather than just a folder inside this repository.

It likely needs a separate repository for:

- preset packaging
- preset-facing documentation
- install and release flow
- example walkthroughs
- validation and compatibility tooling

---

## Usage in a nutshell

```text
Use specify to create new specs.
Use clarify to refine the current spec.
Use next to advance.
Use status to inspect state.
```
