# Spec Impact Analysis

This document defines how AI should analyze a spec for impact.

Impact analysis is not just classification of the spec. It is reconciliation between the spec and the existing project model. The AI must read what already exists before deciding what the spec creates, modifies, removes, or proposes.

The goal is to:

- read the existing project model
- detect where the spec belongs
- decide whether each impact creates new content, modifies existing content, removes it, or proposes app-wide changes
- ask questions when the scope crosses boundaries
- confirm impact before planning

---

## Scope

```text
spec only
  - bug fix
  - refactor
  - local code
  - local tests
  - local contracts

capability
  - add or enhance a system capability
  - capability business requirement
  - local design decision

application
  - business requirement beyond one capability
  - glossary
  - data model
  - architecture rule
  - design rule
  - technology stack

constitution
  - project-level governance
```

---

## Highest Valid Change

Place the change at the highest valid level:

```text
constitution
application
capability
spec only
```

Do not hide a broader decision inside local implementation work.

---

## Clarify-Step Prompt

```text
Analyze this spec for change impact. If the impact is ambiguous or crosses capability, application, or constitution boundaries, ask clarifying questions before planning.
```

Use early impact analysis only when it changes the questions.

---

## Plan-Step Prompt

```text
Before planning, analyze this spec for change impact and identify whether it affects only the spec itself, a capability, the application, or the constitution. If the impact crosses boundaries or is ambiguous, ask clarifying questions instead of guessing.
```

This is the point where impact becomes the working baseline for `plan` and `tasks`.

---

## Read Before Decide

Before assigning impacts, read the relevant parts of the existing model:

```text
.specify/memory/constitution.md
.specify/memory/app/capabilities/
.specify/memory/app/architecture.md
.specify/memory/app/data-model.md
.specify/memory/app/design-rules.md
.specify/memory/app/policies.md
.specify/memory/app/technology-stack.md
.specify/memory/app/business-requirements.md
.specify/memory/app/glossary.md
```

The existing model determines which verb applies:

- `create` — the target does not yet exist
- `modify` — the target exists and the spec changes part of it
- `remove` — the target exists and the spec deletes or supersedes it
- `propose` — the spec changes app-wide truth that needs user-approved promotion before landing in memory

A spec that wants to "add an audit rule" should not blindly emit `create` if a related rule already lives in `.specify/memory/app/policies.md`. The correct call is usually `modify` (refining the existing rule) or `propose` (when the change is broader than the spec's local concerns).

A spec that wants to "add employee onboarding" should not emit `create` for the capability without first checking `.specify/memory/app/capabilities/`. If the capability already exists, the spec is contributing to it (`modify`), not introducing it.

A spec that touches the data-model should not emit a bare `(modify)` — the before-state has existing rows whose migration is part of the work. See the next section.

When the impact list includes `code` or `data-model`, the spec's `Target Areas` must include the project's operational glue (`package.json` or stack equivalent, `db/`, `migrations/`, `db/seeds/` or equivalent, DB connection config, runtime config). Otherwise the spec authorizes building the feature but forbids wiring it up — code lands but won't run.

Without reading the existing model, the AI risks duplicating, contradicting, or silently overwriting decisions the application already made.

---

## Data-Model Impact

Data-model changes have a before-state and an after-state. The spec must capture both, plus the conversion between them. The same shape applies to any structural impact where existing instances must be carried forward.

For a `data-model` impact, record:

- **before** — relevant entities and fields as they exist in `.specify/memory/app/data-model.md`
- **after** — what the spec proposes
- **conversion** — how existing data moves from before to after (backfill rules, type transforms, deprecations, constraint sequencing)

Example:

```text
- data-model: .specify/memory/app/data-model.md (modify)
  before:
    Employee { id, name, hire_date: text }
  after:
    Employee { id, name, hire_date: ISO date, employment_status }
  conversion:
    - parse hire_date text to ISO; flag unparseable rows
    - backfill employment_status = 'active' for existing rows
    - add NOT NULL on employment_status after backfill completes
```

The same before/after/conversion shape applies to:

- `architecture` — subject or boundary moves (existing files relocate)
- `policy` — rule changes that re-classify existing records or decisions
- `technology-stack` — runtime, framework, or storage migrations
- `capability` — capability splits, merges, or scope changes

Without before/after/conversion, `plan` and `tasks` cannot enumerate the migration work, and `implement` ends up inventing it during execution.

---

## Documentation Impact

Documentation has two distinct audiences. mde treats them as separate impact areas:

```text
user-documentation    -> docs/users/<feature>.md     (end-user-facing)
system-documentation  -> docs/system/<feature>.md    (architecture / internals)
```

The two have different content, format, and quality bars. Specs that only need one don't have to produce both.

### When the spec needs user-documentation

If the spec produces visible behavior or affects users:

- new business workflow — step-by-step usage walkthrough
- new UI surface — screenshots when realistic, screen-flow diagram otherwise
- policy change affecting users — plain-language statement of the rule and what changes for the user

### When the spec needs system-documentation

If the spec changes structure, contracts, or shared logic:

- new capability — describe behavior, contracts, dependencies
- new architecture or boundary — describe the structure
- new multi-step workflow — describe the flow

### Diagram requirements

Diagrams are written as Mermaid blocks (text, version-controllable, render natively in GitHub and most IDEs). When the listed trigger impact exists, the corresponding diagram is expected:

| Trigger impact | Diagram | Status |
|---|---|---|
| `data-model` | ERD (entity-relationship) in system docs | **mandatory** |
| `architecture` | Component / boundary diagram in system docs | recommended |
| `code` + multi-step workflow | Sequence diagram in system docs | recommended |
| `capability` with lifecycle | State diagram in system docs | recommended |
| `ui-configuration` with multi-screen flow | Screen-flow diagram in user docs | recommended |
| `ui-configuration` (visible UI) | Screenshots when realistic, otherwise screen-flow diagram | recommended |

When a recommended diagram is omitted, the spec records a one-line justification (e.g. *"single-column policy change, ERD adds no information"*).

The mandatory ERD when `data-model` is in impact exists because data-model changes have the highest cost of being misunderstood downstream — `plan`, `tasks`, and `implement` all read the data shape, and a missing ERD lets each invent its own mental model.

---

## Documentation Ownership

`docs/users/<feature>.md` and `docs/system/<feature>.md` are **owned by the capability** `<feature>`, not by any individual spec. Multiple specs may touch the same capability over time; the doc files are durable application artifacts and persist across specs.

Verb selection for documentation impacts follows the standard rules:

```text
system-documentation: docs/system/<feature>.md (create)   - first spec to document the capability
system-documentation: docs/system/<feature>.md (modify)   - subsequent specs touching the capability
system-documentation: docs/system/<feature>.md (remove)   - capability is being deleted
```

`modify` means *merge new content into the existing file*, never overwrite. The capability's living understanding accumulates across specs; each spec contributes its slice and updates the documentation back-pointer in the capability file (see "Capability documentation back-pointer" below).

The same ownership and verb rules apply to `docs/users/<feature>.md`.

### Capability documentation back-pointer

Each capability file (`.specify/memory/app/capabilities/<feature>.md`) carries a `## Documentation` section that names the doc files and the spec that most recently updated each:

```md
## Documentation
- User: docs/users/<feature>.md (last updated by NNN-spec-name)
- System: docs/system/<feature>.md (last updated by NNN-spec-name)
```

`implement` updates the back-pointer whenever it produces or modifies one of those files. `analyze` reads it to verify the spec actually touched the file when it claimed to.

---

## Derived Documentation Impacts

When a spec declares `capability: <feature> (modify)`, the spec MUST also declare:

- `system-documentation: docs/system/<feature>.md (modify)` — unless the change is purely internal to the capability (no contract surface, no behavior visible to other code or to users), AND a one-line justification is recorded in the spec.
- `user-documentation: docs/users/<feature>.md (modify)` — unless the change has no user-visible effect, AND a one-line justification is recorded in the spec.

When a spec declares `capability: <feature> (create)`:

- `system-documentation: docs/system/<feature>.md (create)` — required, no opt-out.
- `user-documentation: docs/users/<feature>.md (create)` — required if the capability has any user-visible behavior; opt-out requires a one-line justification.

When a spec declares `capability: <feature> (remove)`:

- `system-documentation: docs/system/<feature>.md (remove)` — required.
- `user-documentation: docs/users/<feature>.md (remove)` — required if the file exists.

The opt-out justification mirrors the existing pattern for recommended diagrams: a single line in the spec stating why the doc impact is unnecessary (e.g. *"refactor of internal helpers, no contract or behavior change visible outside the capability"*).

`analyze` enforces these rules as a coverage check. A spec missing a derived doc impact without a justification is `NOT READY`.

---

## Output Shape

Write the result into:

```md
## Change Impact
```

Format:

```text
<impact-area>: <target path> (<verb>)
```

Example:

```text
- capability: .specify/memory/app/capabilities/employee-onboarding.md (modify)
- policy: .specify/memory/app/policies.md (propose)
- code: src/onboarding/ (modify)
- tests: tests/onboarding/ (add)
```

Supported verbs:

```text
create
modify
remove
propose
```

---

## Typical Artifacts By Scope

Spec only usually involves:

```text
spec.md
tasks.md
code
tests
```

Capability usually also involves:

```text
.specify/memory/app/capabilities/<name>.md
plan.md
contracts/
```

Application usually also involves:

```text
.specify/memory/app/*
data-model.md
design-rules.md
architecture.md
technology-stack.md
```

Constitution involves:

```text
.specify/memory/constitution.md
```

---

## Contract Guidance

Use normal Spec Kit artifacts by default. Do not add extra mde artifact types unless normal artifacts cannot carry the responsibility.

Contract folders may include:

```text
contracts/api/
contracts/ui/
contracts/events/
contracts/external/
contracts/read-models/
```
