# Preset Rules

Generated index of every rule in the mde preset. **Do not edit by hand** — run `node tools/list-rules.js` to regenerate.

Source: `preset/PRESET_VALIDATION.md`, `preset/templates/constitution-template-mde-addendum.md`, `preset/commands/next.md`, `preset/commands/*.md`, `preset/defaults/architecture.md`, `preset/defaults/design-rules.md`.

---

## Validation Invariants (17)

From `PRESET_VALIDATION.md`. Each item is a pass condition the preset must satisfy after install.

- **1. Required Files** — every command and template referenced in `preset.yml` exists at the path it claims.
- **2. Command Model** — command files describe creation-only `specify`, refinement-only `clarify`, sequencing-loop `next`, and read-only `status`. No file describes a Business Analysis / App Design / Feature phase model.
- **3. Active-Spec Resolution** — each primitive references `../RESOLUTION.md` rather than restating its own resolution rule.
- **4. Spec Shape** — a freshly generated spec has all four required sections; impact lines parse against the format; no impact references a path outside the declared `Target Areas`.
- **5. Read-Before-Decide** — a spec that touches an existing capability uses `(modify)` not `(create)`; a spec that touches an existing policy uses `(modify)` or `(propose)` not `(create)`.
- **6. Data-Model Impact** — data-model impacts include before/after/conversion notes. Same shape applies to `architecture`, `policy`, `technology-stack`, and `capability` when existing instances must be carried forward.
- **7. Status Inference** — `status.md` contains the inference table; statuses are file-mtime-sensitive (downstream artifacts older than `spec.md` are stale).
- **8. Staleness Gates** — each primitive has an explicit Staleness Gate section.
- **9. Auto-Advance Loop** — `next.md` defines all six stop conditions; promote and implement are inside the loop, not stops.
- **10. Question Lifecycle** — `QUESTION_POLICY.md` documents the full lifecycle and an ownership table per transition.
- **11. Project Memory** — app-memory edits go through `proposed-updates.md` first, not direct writes; `next` includes a step that drains the buffer.
- **12. Capability Catalog** — a capability impact resolves to an actual file path under `capabilities/`; multiple specs may target the same capability over time.
- **13. Branch Mapping** — no command instructs the AI to create a branch for a capability; specs are the unit a branch tracks.
- **14. Runnability** — `implement.md` requires running build and test commands when applicable; `next.md` includes the build-test-failure stop condition.
- **15. Session Accountability (Constitution Principle IX)** — `templates/constitution-template-mde-addendum.md` includes Principle IX with the dual-location rule.
- **16. Forbidden Artifacts** — no command file instructs the AI to create these by default.
- **17. Validation Tools** — validate-preset.js passes after the install.

---

## Constitutional Principles (10)

From `templates/constitution-template-mde-addendum.md`. Project-wide governance principles applied to every spec.

- **I. Spec Kit First** — The project uses normal Spec Kit artifacts as the contract: mde does not create a parallel lifecycle by default.
- **II. Scoped Change Control** — Work is managed as specs: Each spec must declare:
- **III. Durable System Knowledge** — Durable application knowledge belongs in project memory: Capabilities are persistent system behaviors and should live in:
- **IV. No Heavy Trace-Map System by Default** — The project must not create trace-map files by default.
- **V. Contracts Folder Rule** — The `contracts/` folder contains callable surfaces and obligations: A page, route, or view belongs in `contracts/ui/` when another flow or spec depends on it.
- **VI. Highest Valid Change Rule** — When change is needed, apply it at the highest valid level: Do not hide broader decisions inside local implementation work.
- **VII. Controlled AI Context** — The AI engine should not read or modify the whole repository by default.
- **VIII. Validation Gate Rule** — Implementation is not complete until: declared impacts are addressed
- **IX. Session Accountability** — Every material user interaction and AI action is logged in two places: 1. The chronological central log: `logs/<YYYY-MM-DD>.md` 2. The per-spec audit trail: `specs/NNN-change-name/session-log.md` (only when the interaction targets a specific spec)
- **X. Preset Authority** — Any AI agent working in this repository MUST consult the mde preset before taking any action that creates, modifies, or removes files. This applies whether work is invoked via Spec Kit slash commands (`/speckit.*`) or via open-ended chat.

---

## Auto-Advance Stop Conditions (7)

From `commands/next.md`. The `next` loop halts when any of these fire.

- **COMPLETE** — implementation and validation evidence support completion.
- **BLOCKED** — `analyze` reports blockers, or any underlying command returns a blocker.
- **Fresh question file** — a `questions.md` was *created or materially extended in this invocation* with new `proposed` entries. The user must see and approve them; do not auto-approve questions that the user has not yet had a chance to review.
- **Pre-existing unresolved questions in chat-mode** — `questions.md` exists with `proposed` entries from a *prior* invocation and the user has not said `next` since. (When the user invokes `next`, those count as approved per `QUESTION_POLICY.md`; the loop continues.)
- **Underlying command error** — file system or runtime error from a step.
- **Build or test failure** — `implement` ran the project's build or test commands and one returned a non-zero exit code. Halt with `BLOCKED` and report the failing command.
- **Loop guard** — same step would be selected twice in a row without progress (prevents infinite loops on stuck states).

---

## Per-Command Rules (4 sections)

Per-primitive rule sections collected from `commands/*.md`.

### `checklist`

**Universal Rules**

- Do not change `spec.md`, `plan.md`, `tasks.md`, or implementation files unless explicitly asked.
- Create or update checklist files under `checklists/`.
- Use the current spec artifacts as the source of truth.
- Validate against `Change Impact` and `Target Areas`.
- Use the smallest checklist set that gives clear readiness signals.

### `constitution`

**Universal Rules**

- Keep constitution rules short, declarative, and enforceable.
- Do not duplicate one spec's local content.
- Do not duplicate detailed app-memory content unless it is truly constitutional.
- Do not invent rules the user did not ask for.
- When a proposed rule conflicts with an existing rule, ask before overwriting.

### `implement`

**Universal Rules**

- Execute only what is defined by the current spec, plan, and tasks.
- Do not change `spec.md` intent unless explicitly instructed.
- Do not invent behavior outside the confirmed impacts.
- Keep changes within target areas.
- Record progress in `tasks.md`.
- Update validation evidence when validation is performed.
- When `code` or `tests` impacts exist, run the project's build and test commands. Non-zero exit codes are blockers.
- When `code` or `data-model` impacts exist, leave the project runnable: `start`/`dev`, `build`, `test`, and (for `data-model`) `db:create`/`db:migrate`/`db:seed` scripts must exist and exit 0. DB connection config (`.env`, `config/`) must be wired so scripts can actually connect.
- When `user-documentation` impact exists, produce `docs/users/<feature>.md`. When `system-documentation` impact exists, produce `docs/system/<feature>.md`. If `data-model` is in impact, system docs must include an ERD (Mermaid). See `IMPACT_ANALYSIS.md` for the full diagram-trigger table.
- When producing or modifying `docs/users/<feature>.md` or `docs/system/<feature>.md`, also update the `## Documentation` section of `.specify/memory/app/capabilities/<feature>.md` so the `last updated by NNN-spec-name` back-pointer matches the active spec. For `(modify)` verbs, **merge** new content into the existing doc file — do not overwrite it. See `IMPACT_ANALYSIS.md` Documentation Ownership for the full rule.

### `next`

**Universal rules**

- Be short and concrete.
- Use existing artifacts as evidence.
- Respect `preset/QUESTION_POLICY.md`.
- Do not mutate a different spec than the active one.
- Do not create a new sibling spec through `next`.
- Use `questions.md` when multiple questions exist.
- Use `Change Impact` and `Target Areas` as the main planning surface.

---

## Architecture Defaults (5)

From `defaults/architecture.md`. Recommended structural rules for the default `web-monolith` profile. Alternative profiles in `defaults/profiles/` select different subsets.

- **AR-001 Modular Monolith** — Default system style. One deployable application, structured into modules with clear boundaries.
- **AR-002 Layered Application** — Default structural model: UI/API → Application/Service → Domain → Infrastructure. Each layer depends only on layers below it.
- **AR-003 MVC + REST API** — Default interaction model: server-rendered Model-View-Controller for staff/internal UIs, plus REST-style JSON APIs for integrations and progressive enhancement. Same application process serves both.
- **AR-004 Repository Pattern** — Use the Repository pattern when persistence complexity or aggregate access patterns justify the abstraction. Avoid ceremonial repositories for trivial CRUD.
- **AR-005 Command-Query Separation** — Conceptually separate commands (state-changing) from queries (read-only). Full CQRS (separate read/write models) only when read and write paths materially diverge.

---

## Design Defaults (16)

From `defaults/design-rules.md`. Recommended cross-cutting design rules for the default `web-monolith` profile.

- **DR-001 Validation at Domain Layer** — Enforce business invariants in the domain or application/domain boundary, not only in UI or transport validation. Transport validation catches malformed input; domain validation catches invalid state transitions.
- **DR-002 Data-Access Control via Repositories** — Restrict persistence access to approved repositories or data-access services within the architecture's boundaries. UI and unrelated layers must not issue arbitrary queries.
- **DR-003 Transaction Boundaries** — One business command commits one aggregate-consistent unit of work. Cross-aggregate side effects use events or async follow-up, not nested transactions.
- **DR-004 Optimistic Locking** — Use version-based optimistic locking on mutable business records. Conflict resolution surfaces to the user; do not silently overwrite.
- **DR-005 Audit Trail** — Record create, state-transition, approval, reassignment, and override actions with actor, timestamp, and reason when applicable. Audit data is durable — do not delete.
- **DR-006 Structured Logging** — Use structured application logs with correlation IDs. Log workflow transitions, integration calls, validation failures, and unexpected errors. Never log sensitive data.
- **DR-007 Referential Integrity** — Enforce foreign keys within the same service-owned persistence boundary. Cross-boundary references use IDs without DB-level FK enforcement.
- **DR-008 Surrogate IDs + Business Keys** — Use surrogate technical IDs internally (`UUID`, `bigint`). Use explicit business keys for human/business uniqueness (`employee_id`, `invoice_number`). Never expose surrogate IDs to users when business keys exist.
- **DR-009 Date Display** — Use short date format (`YYYY-MM-DD` or locale-short) by default. Show time only when business-significant. Never display ISO-8601 raw timestamps to end users.
- **DR-010 System and Audit Field Visibility** — Hide system-generated fields (surrogate IDs, audit timestamps, version numbers) by default in lists, details, and forms. Show on request via an explicit reveal control.
- **DR-011 Foreign Reference Display** — Do not display foreign keys directly to users. Show the related record's business key or business label in lists, details, and forms.
- **DR-012 Related Entity Selection — Dropdown** — Use a dropdown that displays all related-entity names when the candidate set is small enough for fast visual selection (typically under ~50 items).
- **DR-013 Related Entity Selection — Popup Lookup** — Use a popup entity lookup with multiple descriptive fields and filtering when users need richer context to choose a related entity (typically ~50 to several hundred items).
- **DR-014 Related Entity Selection — Search Required** — Require the user to enter search criteria before querying when the candidate set is too large for full preloading (typically thousands or more).
- **DR-015 Full E2E Test and Coverage** — Generate full end-to-end tests using real database connections (not mocks). Provide coverage results as validation evidence. Mocking is allowed for external services that can't be safely run in tests.
- **DR-016 Feature User and System Documentation** — Every feature produces both:

---

## Other rule sources

Rules that don't lend themselves to extraction (tabular, prose-heavy, or formatted as algorithms) live in their authoring files:

- [`RESOLUTION.md`](RESOLUTION.md) — active-spec resolution algorithm
- [`QUESTION_POLICY.md`](QUESTION_POLICY.md) — question lifecycle and ownership table
- [`commands/status.md`](commands/status.md) — status inference table
- [`IMPACT_ANALYSIS.md`](IMPACT_ANALYSIS.md) — read-before-decide, highest valid change, data-model before/after, documentation impact, diagram triggers

