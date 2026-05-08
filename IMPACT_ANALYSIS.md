# Spec Impact Analysis

How AI analyzes a spec for impact: reconcile the spec against the existing project model, then decide what each impact creates, modifies, removes, or proposes. Read what already exists before assigning verbs — guessing duplicates, contradicts, or silently overwrites prior decisions.

The output of this analysis is the spec's `## Change Impact` section, which becomes the working baseline for `plan`, `tasks`, `analyze`, and `implement`.

---

## Scope hierarchy

Place every change at the **highest valid level**. Do not hide a broader decision inside local implementation work.

```text
constitution   — project-level governance
                 → .specify/memory/constitution.md

application    — truth that crosses capabilities
                 → .specify/memory/app/{architecture,data-model,design-rules,
                                       policies,technology-stack,
                                       business-requirements,glossary}.md

capability     — one system capability, including its business requirement
                 and local design decisions
                 → .specify/memory/app/capabilities/<name>.md
                 → docs/system/<name>.md, docs/users/<name>.md
                 → contracts/, plan.md

spec only      — bug fix, refactor, local code, local tests, local contracts
                 → spec.md, tasks.md, source, tests
```

When in doubt about which level a change belongs at, ask before planning.

---

## Verbs

| Verb | Meaning |
|---|---|
| `create` | Target does not yet exist. |
| `modify` | Target exists; spec changes part of it. For doc files, **merge** new content — never overwrite. |
| `remove` | Target exists; spec deletes or supersedes it. |
| `propose` | App-wide change that needs user-approved promotion before landing in memory. |

---

## Read before decide

Before assigning verbs, read the relevant parts of the existing model:

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

The existing model determines verb selection:

- "Add an audit rule" → check `policies.md`. If a related rule exists, use `modify` (refining) or `propose` (broader than the spec). Don't blindly `create`.
- "Add employee onboarding" → check `capabilities/`. If the capability already exists, use `modify`, not `create`.
- A `data-model` impact is never bare — see [Structural impacts](#structural-impacts-beforeafterconversion) below.

---

## Output shape

Write the result into `## Change Impact` in the spec. Format:

```text
<impact-area>: <target path> (<verb>)
```

Example:

```text
- capability: .specify/memory/app/capabilities/employee-onboarding.md (modify)
- policy: .specify/memory/app/policies.md (propose)
- code: src/onboarding/ (modify)
- tests: tests/onboarding/ (modify)
- system-documentation: docs/system/employee-onboarding.md (modify)
```

When `code` or `data-model` is in the impact list, `Target Areas` MUST also include the project's operational glue (`package.json` or stack equivalent, `db/`, `migrations/`, `db/seeds/`, DB connection config, runtime config). Otherwise the spec authorizes building the feature but forbids wiring it up — code lands but won't run.

---

## Structural impacts (before/after/conversion)

When the impact carries existing instances forward, record three states, not just the after:

- **before** — relevant entities/fields/rules as they exist today
- **after** — what the spec proposes
- **conversion** — how existing content moves from before to after (backfill, type transforms, deprecations, constraint sequencing)

Applies to: `data-model`, `architecture`, `policy`, `technology-stack`, `capability` (when splitting, merging, or rescoping).

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

Without before/after/conversion, `plan` and `tasks` cannot enumerate the migration work, and `implement` invents it during execution.

---

## Documentation impact

Documentation has two audiences, treated as separate impact areas:

| Impact area | Path | Audience | Trigger |
|---|---|---|---|
| `user-documentation` | `docs/users/<feature>.md` | End users | Visible behavior, UI surface, or user-facing policy change. |
| `system-documentation` | `docs/system/<feature>.md` | Engineers | Structure, contracts, shared logic, multi-step workflow. |

Specs that need only one don't have to produce both.

### Diagram requirements

Diagrams are Mermaid blocks (text, version-controllable, render in GitHub and most IDEs).

| Trigger impact | Diagram | Status |
|---|---|---|
| `data-model` | ERD in system docs | **mandatory** |
| `architecture` | Component / boundary diagram in system docs | recommended |
| `code` + multi-step workflow | Sequence diagram in system docs | recommended |
| `capability` with lifecycle | State diagram in system docs | recommended |
| `ui-configuration` with multi-screen flow | Screen-flow diagram in user docs | recommended |
| `ui-configuration` (visible UI) | Screenshots when realistic, otherwise screen-flow diagram | recommended |

When a recommended diagram is omitted, record a one-line justification in the spec (e.g. *"single-column policy change, ERD adds no information"*). The mandatory ERD when `data-model` is in impact exists because data-model misunderstandings cascade into `plan`, `tasks`, and `implement` — each invents its own mental model in the absence of a shared one.

---

## Documentation ownership

`docs/users/<feature>.md` and `docs/system/<feature>.md` are **owned by the capability** `<feature>`, not by any individual spec. Multiple specs touch the same capability over time; the doc files are durable application artifacts.

Capability files (`.specify/memory/app/capabilities/<feature>.md`) carry a `## Documentation` back-pointer:

```md
## Documentation
- User: docs/users/<feature>.md (last updated by NNN-spec-name)
- System: docs/system/<feature>.md (last updated by NNN-spec-name)
```

`implement` updates the back-pointer whenever it touches one of those files. `analyze` reads it to verify the spec actually touched the file when it claimed to.

### Derived documentation impacts

When a spec touches a capability, it MUST also declare the corresponding doc impact (or record a one-line justification for opting out):

| Capability verb | Required doc impact | Opt-out condition |
|---|---|---|
| `(create)` | `system-documentation: ... (create)` | Never. |
| `(create)` | `user-documentation: ... (create)` | Capability has no user-visible behavior. |
| `(modify)` | `system-documentation: ... (modify)` | Change is purely internal — no contract or behavior visible outside the capability. |
| `(modify)` | `user-documentation: ... (modify)` | Change has no user-visible effect. |
| `(remove)` | `system-documentation: ... (remove)` | Never. |
| `(remove)` | `user-documentation: ... (remove)` | The user-doc file does not exist. |

`analyze` enforces these as a coverage check. A spec missing a derived doc impact without a justification is `NOT READY`.

---

## When this analysis runs

Two insertion points, same analysis:

- **`clarify`** — when the impact is ambiguous or crosses capability/application/constitution boundaries, ask clarifying questions before planning. Use early impact analysis only when it changes the questions.
- **`plan`** — before producing the plan, run impact analysis as the working baseline. If the impact crosses boundaries or remains ambiguous, ask clarifying questions instead of guessing.

---

## Contract folders

Contracts hold callable surfaces and obligations. Use normal Spec Kit artifacts by default; do not add extra mde artifact types unless normal artifacts cannot carry the responsibility.

```text
contracts/api/          - HTTP / RPC endpoints
contracts/ui/           - pages, routes, views depended on by other flows
contracts/events/       - emitted/consumed events
contracts/external/     - third-party APIs the project calls
contracts/read-models/  - query-side projections
```

Internal helpers and private components are not contracts.
