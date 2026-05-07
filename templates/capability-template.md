# Capability - <Capability Name>

Capabilities are durable system behaviors. A capability outlives any single spec; multiple specs may contribute to it over time.

This file lives at `.specify/memory/app/capabilities/<feature>.md`. It is application-scope project memory, not spec-scope. mde-native — no upstream Spec Kit counterpart.

---

## Summary

One paragraph stating what the capability does and what business problem it solves. Written for someone reading the catalog, not implementing the feature.

## Boundaries

What this capability owns, and what it explicitly does not own. Use this to prevent scope drift across specs.

- **Owns:** ...
- **Does not own:** ... (point to the capability that does)

## Contracts

The integration surfaces this capability exposes. Same vocabulary as `specs/NNN-*/contracts/`:

- API endpoints — `contracts/api/...`
- UI routes — `contracts/ui/...`
- Events — `contracts/events/...`
- External integrations — `contracts/external/...`
- Read models — `contracts/read-models/...`

Reference the canonical contract files; do not duplicate their content here.

## Data Model

The entities this capability owns. Cross-link to `.specify/memory/app/data-model.md` for full schema; this section names which entities belong to this capability.

## Dependencies

Other capabilities or external systems this capability depends on.

- **Internal:** other capabilities by name
- **External:** named third-party systems, infrastructure, or contracts

## Documentation

Application-level docs for this capability. `implement` updates the `last updated by` lines whenever it produces or modifies the corresponding doc file. `analyze` reads these to verify spec→doc coverage.

- **User:** `docs/users/<feature>.md` (last updated by `NNN-spec-name`)
- **System:** `docs/system/<feature>.md` (last updated by `NNN-spec-name`)
- **Diagrams:** list any standalone diagram files (e.g. `docs/system/<feature>-erd.md`) and which spec last updated each. The ERD is mandatory when this capability owns data-model entities.

If a doc file does not yet exist, mark it `(not yet created — see spec NNN)` rather than omitting the line.

## Spec History

Append-only list of specs that have contributed to this capability. Newest first.

- `NNN-spec-name` — one-line summary of what this spec changed about the capability
- `NNN-earlier-spec` — ...

`implement` appends an entry whenever the active spec lists this capability in `Change Impact`.

## Open Questions

Capability-level questions that span specs. Per-spec questions live in `specs/NNN-*/questions.md`, not here. If a question turns out to be capability-scope, promote it from the spec's `questions.md` into this section via `clarify` or `next`.

## Notes

Free-form notes on the capability that don't fit the structured sections. Decisions, deferred work, internal rationale that isn't a contract or a doc.
