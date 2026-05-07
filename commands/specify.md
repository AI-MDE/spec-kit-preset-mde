# mde: Specify

Create a new scoped change spec.

`specify` is the creation command. It creates a new spec folder for new work. It does not refine an existing spec.

User-facing workflow:

```text
specify -> next -> status
```

Terminology note:

```text
Spec Kit "feature" -> spec / scoped work item
mde capability  -> durable system behavior
```

If the user wants to refine an existing spec, they should switch to the relevant branch and use `clarify`.

---

## 1. Core Model

mde treats meaningful work as scoped change control.

A scoped change lives under:

```text
specs/NNN-change-name/
```

It may target source code, docs, config, project memory, or the constitution.

Persistent project memory lives under:

```text
.specify/memory/app/
```

Project governance lives in:

```text
.specify/memory/constitution.md
```

---

## 2. Creation-Only Rule

`specify` means:

```text
create a new scoped change
```

Do not use `specify` to revise, resume, or mutate an existing spec.

If the user's words indicate refinement of existing work, such as:

```text
refine
revise
update the current spec
change this existing spec
fix the spec
clarify this feature
```

then do not edit the existing spec through `specify`.

Instead:

- tell the user to switch to the relevant branch if needed
- recommend `clarify` for spec refinement
- only create a new sibling spec if the user explicitly wants new work

If the request closely matches an existing spec, warn about the match before creating a duplicate.

---

## 3. First-Run Behavior

If no scoped spec folders exist yet and the user uses `specify`, create an initial setup-oriented spec unless the user explicitly asks for some other new spec.

Create:

```text
specs/000-initial-project-setup/
```

The initial setup spec targets:

```text
.specify/memory/constitution.md
.specify/memory/app/
```

Detect greenfield vs brownfield by inspecting the working tree:

- **Greenfield** (no source code, no manifests, no schema). The setup spec gathers business intent and proposed memory from the user's description. Add a question file when scope is vague.

  ```md
  ## Change Impact
  - constitution: .specify/memory/constitution.md (create)
  - memory: .specify/memory/app/ (create)

  ## Target Areas
  - .specify/memory/constitution.md
  - .specify/memory/app/
  ```

- **Brownfield** (existing source code, package manifests, or database schema present). The setup spec must include a "Reverse-Engineer Memory" task. Inspection proposes memory from code:

  - capabilities derived from cohesive surfaces (routes, services, modules), each citing the source files
  - technology stack from `package.json`, `requirements.txt`, `go.mod`, etc.
  - architecture from folder layout and import graph
  - recurring patterns flagged as candidate design rules
  - data model from schema files or migrations

  All inferences are written to `.specify/memory/app/proposed-updates.md` first, marked as proposed with source citations. `next` promotes confirmed items into the appropriate memory files on user approval.

  ```md
  ## Change Impact
  - constitution: .specify/memory/constitution.md (create)
  - memory: .specify/memory/app/proposed-updates.md (propose)
  - capability: .specify/memory/app/capabilities/* (propose)
  - technology-stack: .specify/memory/app/technology-stack.md (propose)
  - architecture: .specify/memory/app/architecture.md (propose)
  - design-rule: .specify/memory/app/design-rules.md (propose)

  ## Target Areas
  - .specify/memory/constitution.md
  - .specify/memory/app/
  ```

Do not treat first-run app setup as normal capability work. Do not silently overwrite memory; always propose first.

### First-run question set

Before promoting anything to project memory, ask the user a small set of setup questions. Write them to `specs/000-initial-project-setup/questions.md` so the user sees real menus, not hidden fields.

**Greenfield questions (Q1–Q6):**

```md
### Q1. Which architecture profile fits this project?

Default answer:
web-monolith

Options:
- web-monolith (default) — server-rendered + REST API in one process; internal business apps
- microservices — multiple services with bounded contexts; distributed systems
- event-driven — event sourcing or pub/sub patterns; async workflows
- api-only — headless backend with no UI in this codebase
- cli-tool — command-line tool; no UI, often no DB

Rationale:
web-monolith fits most internal business apps. Pick another profile only if the project's shape is fundamentally different.

Impact if changed:
A different AR/DR rule set will be pre-populated in spec.md's Application Profile section from preset/defaults/profiles/<name>.md.

Status:
proposed

### Q2. Project name (app title)?

Default answer:
<repository folder name>

Rationale:
The constitution and user-facing documentation reference the project by name. Pick something more specific than the repo folder name if the two should differ.

Impact if changed:
Replaces `[PROJECT_NAME]` placeholders in the constitution.

Status:
proposed

### Q3. Business domain?

Default answer:
general business application

Rationale:
A short business-domain phrase (e.g., "HR management", "IT consulting", "e-commerce", "accounting") helps later capability inference and clarifies scope. This is business domain, not DDD bounded contexts.

Impact if changed:
Pinned in the constitution's Domain Context section. Influences capability naming and out-of-scope decisions.

Status:
proposed

### Q4. Primary users?

Default answer:
internal staff

Options:
- internal staff
- external customers
- developers
- mixed

Rationale:
Different audiences imply different defaults for auth, audit, and UI ergonomics.

Impact if changed:
Pinned in the constitution's Domain Context section.

Status:
proposed

### Q5. Compliance or regulatory context?

Default answer:
none

Options:
- none
- GDPR
- HIPAA
- PCI
- SOX
- other (describe)

Rationale:
Regulatory context affects audit, logging, retention, and data-handling rules. Stating "none" is itself a recorded decision.

Impact if changed:
Recorded in the constitution's Constraints section.

Status:
proposed

### Q6. Accept the default AR/DR rule set for the chosen profile?

Default answer:
accept

Options:
- accept — pre-populate spec.md's Application Profile with the profile's AR and DR rules; the user can still uncheck or edit individual rules in-spec before `next`
- reject — leave the Application Profile empty; rules can be added later via subsequent specs

Rationale:
Most projects benefit from the default rule set. Reject only if the project's fit is materially different from any profile, or the team explicitly wants to author rules from scratch.

Impact if changed:
On reject, `.specify/memory/app/architecture.md` and `.specify/memory/app/design-rules.md` remain empty after promotion.

Status:
proposed
```

**Brownfield questions:**

Brownfield uses Q1–Q5 from the greenfield set, but with codebase-derived defaults — for example Q2 from `package.json` `name`, Q1 from the observed shape (presence of UI, API surface, services). Brownfield does *not* use Q6: inferred AR/DR rules are written per-rule to `.specify/memory/app/proposed-updates.md`, where the user accepts or rejects each one individually.

When the user says `next` (per QUESTION_POLICY), proposed answers count as approved. If the user edited an answer before saying `next`, use the edited answer.

### Promotion on `next`

After Q1 is confirmed and (greenfield) Q6 is accepted, populate spec.md's `## Application Profile` section from:

```text
preset/defaults/profiles/<chosen-profile>.md     (which AR/DR rules apply)
preset/defaults/architecture.md                  (AR rule definitions)
preset/defaults/design-rules.md                  (DR rule definitions)
preset/defaults/technology-stack.md              (default tech defaults — may differ per profile)
```

The Application Profile section uses checkbox lists. To remove a rule, the user unchecks it. To edit wording or scope, the user edits the line in place. On `next`, checked rules promote to `.specify/memory/app/architecture.md` and `.specify/memory/app/design-rules.md`.

Q2–Q5 promote to the constitution: Q2 replaces `[PROJECT_NAME]`, Q3–Q5 populate the Domain Context and Constraints sections.

---

## 4. Existing-Work Conflict Rule

Before creating a new spec, check whether the request appears to target work that already exists.

If there is one clear existing match:

- report the existing spec folder
- do not edit it
- recommend `clarify` if the user intends refinement
- create a new sibling spec only if the user confirms that intent

If there are several likely existing matches:

- list the matches
- ask whether the user wants to refine one with `clarify`
- or confirm creating a new sibling spec

`specify` must not silently mutate an existing spec.

---

## 5. Change Impact Inference

Every new scoped change spec should include a `## Change Impact` section.

Use the user's words and likely target paths to infer impacts.

Examples:

```text
specify employee onboarding feature
```

Change impact:

```text
- capability: .specify/memory/app/capabilities/employee-onboarding.md (create)
- code: src/onboarding/ (create)
- tests: tests/onboarding/ (create)
```

```text
specify change theme colors
```

Change impact:

```text
- ui-configuration: src/theme/ (modify)
```

```text
specify define application architecture
```

Change impact:

```text
- architecture: .specify/memory/app/architecture.md (modify)
- design-rule: .specify/memory/app/design-rules.md (modify)
```

```text
specify change technology stack to FastAPI + React
```

Change impact:

```text
- technology-stack: .specify/memory/app/technology-stack.md (modify)
- code: src/ (modify)
- tests: tests/ (verify)
```

---

## 6. Target Areas

Every `spec.md` should include target areas.

Required sections:

```md
## Change Impact

## Target Areas

## Out of Scope
```

Target areas define what may change.

Examples:

```text
src/
tests/
docs/
config/
specs/NNN-*/
.specify/memory/constitution.md
.specify/memory/app/
```

Do not modify files outside the target areas without explicit approval.

---

## 7. Project Memory Rule

Project memory changes must happen through scoped change control.

A scoped change may propose or apply changes to:

```text
.specify/memory/app/business-requirements.md
.specify/memory/app/glossary.md
.specify/memory/app/architecture.md
.specify/memory/app/data-model.md
.specify/memory/app/design-rules.md
.specify/memory/app/policies.md
.specify/memory/app/technology-stack.md
.specify/memory/app/feature-boundaries.md
.specify/memory/app/contracts/
.specify/memory/app/proposed-updates.md
.specify/memory/app/work-state.md
```

A spec may discover app-wide knowledge, but it should propose app updates rather than silently hiding app-wide decisions inside the spec folder.

---

## 8. Contracts vs App-Wide Rules

Spec-owned contracts live inside the scoped change folder:

```text
specs/NNN-change-name/contracts/
```

Contracts define integration surfaces:

- API endpoints
- UI routes
- events
- read models
- external integrations

App-wide rules live in project memory:

```text
.specify/memory/app/design-rules.md
.specify/memory/app/policies.md
```

Do not confuse spec-owned contracts with app-wide governance rules.

---

## 9. Branching Rule

Git branches should align with scoped changes.

Conceptual mapping:

```text
git branch -> specs/NNN-change-name/ -> target areas
```

A branch is not limited to capability work.

Branches may represent:

- setup changes
- capability work
- UI changes
- architecture changes
- policy changes
- bug fixes
- refactors

For existing work, branch switching is handled by Git, not by `specify`.

---

## 10. Questions Rule

When multiple questions exist, write them to:

```text
specs/NNN-change-name/questions.md
```

or, for app memory questions:

```text
.specify/memory/app/questions.md
```

Each question should include:

- question
- default or proposed answer
- rationale
- impact if changed
- status

If the user says `next`, proposed answers are treated as approved unless the user explicitly rejects or edits them.

---

## 11. Required `spec.md` Shape

Create `spec.md` with:

```md
# Spec - <Change Name>

## Change Impact

## Summary

## Purpose / Outcome

## Target Areas

## Out of Scope

## Current Context

## Application Profile

## Requirements

## Contracts / Integration Surfaces

## App Memory Impact

## Questions

## Acceptance / Completion Criteria
```

Use only sections that are relevant, but do not omit change impact, target areas, or out-of-scope.

`## Application Profile` is required only on the first-run setup spec (`000-initial-project-setup/`) when greenfield Q6 is `accept`. It lists the proposed AR/DR rules as checkboxes so the user can uncheck or edit individual rules before `next` promotes the checked ones to project memory:

```md
## Application Profile

Profile: <profile-name>   (from Q1)
Source: preset/defaults/profiles/<profile-name>.md

### Architecture Rules
- [x] AR-001 Modular Monolith
- [x] AR-002 Layered Application
- [x] AR-003 MVC + REST API
- [x] AR-004 Repository Pattern
- [x] AR-005 Command-Query Separation

### Design Rules
- [x] DR-001 Validation at Domain Layer
- [x] DR-002 Data-Access Control via Repositories
- [x] DR-003 Transaction Boundaries
... (full list per profile)

To remove a rule, uncheck it. To edit wording or scope, edit the line in place.
On `next`, checked rules promote to `.specify/memory/app/architecture.md` and `.specify/memory/app/design-rules.md`.
```

Subsequent specs do not include Application Profile unless they explicitly target architecture or design-rule changes.

---

## 12. Final Output Rule

After running `specify`, report:

```text
Created: <spec folder>
Change impacts: <impact list>
Target areas: <paths>
Questions file: <path or none>
Next: say `next` to advance this scoped change
```

Do not implement during `specify`.
