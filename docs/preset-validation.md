# Preset Validation

Checklist used after installing the mde preset into a Spec Kit project. Every item should pass before treating the install as ready.

Expected install location:

```text
.specify/presets/mde/
```

---

## 1. Required Files

```text
preset.yml
README.md
RULES.md
RESOLUTION.md
QUESTION_POLICY.md
PRESET_VALIDATION.md
IMPACT_ANALYSIS.md
docs/
  README.md
  terminology.md
  capabilities-vs-specs.md
  user-cases.md
commands/
  specify.md
  clarify.md
  next.md
  status.md
  setup.md
  plan.md
  tasks.md
  analyze.md
  checklist.md
  implement.md
  constitution.md
templates/
  spec-template-mde-addendum.md
  plan-template-mde-addendum.md
  tasks-template-mde-addendum.md
  checklist-template-mde-addendum.md
  constitution-template-mde-addendum.md
  capability-template.md
defaults/
  architecture.md
  design-rules.md
  technology-stack.md
  profiles/
    web-monolith.md
    api-only.md
    cli-tool.md
    event-driven.md
    microservices.md
```

**Pass condition**: every command and template referenced in `preset.yml` exists at the path it claims.

---

## 2. Command Model

User-facing commands:

```text
specify  -> create a new spec
clarify  -> refine the active spec
next     -> advance the active spec, looping until COMPLETE / BLOCKED / fresh questions
status   -> read-only project state
setup    -> thin alias for specify on first run
```

Internal primitives invoked by `next`:

```text
plan, tasks, analyze, implement, checklist (on demand), constitution
```

**Pass condition**: command files describe creation-only `specify`, refinement-only `clarify`, sequencing-loop `next`, and read-only `status`. No file describes a Business Analysis / App Design / Feature phase model.

---

## 3. Active-Spec Resolution

Every primitive that operates on one spec (clarify, plan, tasks, analyze, checklist, implement, next) defers to `RESOLUTION.md` for target selection.

**Pass condition**: each primitive references `../RESOLUTION.md` rather than restating its own resolution rule.

---

## 4. Spec Shape

Every generated `spec.md` must include:

```md
## Summary
## Change Impact
## Target Areas
## Out of Scope
```

`## Change Impact` entries follow:

```text
<impact-area>: <target path> (<verb>)
```

Verbs: `create`, `modify`, `remove`, `propose`. Areas drawn from the impact-area vocabulary in `IMPACT_ANALYSIS.md`.

**Pass condition**: a freshly generated spec has all four required sections; impact lines parse against the format; no impact references a path outside the declared `Target Areas`.

---

## 5. Read-Before-Decide

When a spec is created, the AI must read existing project memory before assigning impacts. Specifically:

```text
.specify/memory/constitution.md
.specify/memory/app/capabilities/
.specify/memory/app/architecture.md
.specify/memory/app/data-model.md
.specify/memory/app/design-rules.md
.specify/memory/app/policies.md
.specify/memory/app/technology-stack.md
```

**Pass condition**: a spec that touches an existing capability uses `(modify)` not `(create)`; a spec that touches an existing policy uses `(modify)` or `(propose)` not `(create)`.

---

## 6. Data-Model Impact

When `data-model` is in the impact list, the spec records:

```text
before: <existing entities/fields>
after:  <proposed entities/fields>
conversion: <migration steps>
```

**Pass condition**: data-model impacts include before/after/conversion notes. Same shape applies to `architecture`, `policy`, `technology-stack`, and `capability` when existing instances must be carried forward.

---

## 7. Status Inference

Status is inferred mechanically per the table in `commands/status.md`. Two AI sessions on the same spec produce the same status.

**Pass condition**: `status.md` contains the inference table; statuses are file-mtime-sensitive (downstream artifacts older than `spec.md` are stale).

---

## 8. Staleness Gates

Primitives refuse to operate against stale upstream artifacts:

- `plan` recommends `clarify` if spec lacks Change Impact / Target Areas / Out of Scope
- `tasks` recommends `plan` if `plan.md` is missing or older than `spec.md`
- `analyze` refuses to certify ready if `plan.md` or `tasks.md` is stale
- `implement` refuses to execute if `plan.md` or `tasks.md` is stale
- `checklist` produces only spec-readiness checks against stale artifacts

**Pass condition**: each primitive has an explicit Staleness Gate section.

---

## 9. Auto-Advance Loop

`next` chains clarify → plan → tasks → analyze → promote → implement until a stop condition fires:

```text
COMPLETE | BLOCKED | fresh-questions | command-error | build-test-failure | loop-guard
```

**Pass condition**: `next.md` defines all six stop conditions; promote and implement are inside the loop, not stops.

---

## 10. Question Lifecycle

`questions.md` follows the lifecycle in `QUESTION_POLICY.md`:

```text
proposed -> approved -> applied -> closed
         \-> rejected -> closed
         \-> blocked  -> resolve before progress
         \-> deferred
```

**Pass condition**: `QUESTION_POLICY.md` documents the full lifecycle and an ownership table per transition.

---

## 11. Project Memory

Persistent app-wide knowledge lives in:

```text
.specify/memory/app/
  capabilities/
  architecture.md
  business-requirements.md
  data-model.md
  design-rules.md
  glossary.md
  policies.md
  technology-stack.md
  proposed-updates.md
  work-state.md
```

`proposed-updates.md` drains during `next`'s promote step.

**Pass condition**: app-memory edits go through `proposed-updates.md` first, not direct writes; `next` includes a step that drains the buffer.

---

## 12. Capability Catalog

```text
.specify/memory/app/capabilities/<name>.md
```

One file per capability. The catalog defines what the application *is*; specs are the work performed against it.

**Pass condition**: a capability impact resolves to an actual file path under `capabilities/`; multiple specs may target the same capability over time.

---

## 13. Branch Mapping

Branches map to specs, not to capabilities.

```text
git branch <-> specs/NNN-change-name/
capability  <-> long-lived, no branch of its own
```

**Pass condition**: no command instructs the AI to create a branch for a capability; specs are the unit a branch tracks.

---

## 14. Runnability

When `code` or `data-model` is in the impact list, implementation produces runnable artifacts:

```text
build script (e.g. npm run build)
test script (e.g. npm test)
start/dev script
db:create / db:migrate (when data-model changes)
```

Non-zero exit codes are blockers, not warnings.

**Pass condition**: `implement.md` requires running build and test commands when applicable; `next.md` includes the build-test-failure stop condition.

---

## 15. Session Accountability (Constitution Principle IX)

When a project adopts the mde constitution addendum, every material interaction is logged in:

```text
logs/<YYYY-MM-DD>.md                   (chronological, all entries)
specs/NNN-change-name/session-log.md   (per-spec mirror, when spec-bound)
```

Each entry: timestamp, actor, interaction, outcome, tokens, size.

**Pass condition**: `templates/constitution-template-mde-addendum.md` includes Principle IX with the dual-location rule.

---

## 16. Forbidden Artifacts

By default, mde does not create:

```text
trace-map files
implementation-contract files
JSON companion files (other than .specify/feature.json which is upstream)
parallel mde folders outside the normal Spec Kit layout
```

**Pass condition**: no command file instructs the AI to create these by default.

---

## 17. Validation Tools

```bash
node tools/validate-preset.js
```

**Pass condition**: validate-preset.js passes after the install.

---

## Failure conditions

The preset fails validation if:

- a referenced command or template file is missing
- any command file describes the old Business Analysis / App Design / Feature phase model
- the resolution rule is duplicated in primitives instead of referencing `RESOLUTION.md`
- the status inference table is missing from `status.md`
- `next.md` lacks the auto-advance loop and stop conditions
- the question lifecycle is not documented in `QUESTION_POLICY.md`
- a generated spec lacks `## Change Impact` or `## Target Areas`
- the AI invents trace-map or JSON companion files by default
- implementation runs against stale upstream artifacts without a Staleness Gate refusal
