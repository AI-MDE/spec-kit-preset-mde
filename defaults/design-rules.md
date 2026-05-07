# Design Rule Defaults

Recommended cross-cutting design rules (DR-###) the mde preset proposes for new projects. These are *defaults*, not enforced rules. First-run `specify` proposes them in the Application Profile; the user accepts, modifies, or rejects each one. Accepted rules promote to `.specify/memory/app/design-rules.md`.

Architecture-level decisions live in `architecture.md` (AR-###). DR rules are policies and patterns that apply *within* the chosen architecture.

---

## Persistence and consistency

### DR-001 Validation at Domain Layer

Enforce business invariants in the domain or application/domain boundary, not only in UI or transport validation. Transport validation catches malformed input; domain validation catches invalid state transitions.

### DR-002 Data-Access Control via Repositories

Restrict persistence access to approved repositories or data-access services within the architecture's boundaries. UI and unrelated layers must not issue arbitrary queries.

### DR-003 Transaction Boundaries

One business command commits one aggregate-consistent unit of work. Cross-aggregate side effects use events or async follow-up, not nested transactions.

### DR-004 Optimistic Locking

Use version-based optimistic locking on mutable business records. Conflict resolution surfaces to the user; do not silently overwrite.

### DR-005 Audit Trail

Record create, state-transition, approval, reassignment, and override actions with actor, timestamp, and reason when applicable. Audit data is durable — do not delete.

### DR-006 Structured Logging

Use structured application logs with correlation IDs. Log workflow transitions, integration calls, validation failures, and unexpected errors. Never log sensitive data.

### DR-007 Referential Integrity

Enforce foreign keys within the same service-owned persistence boundary. Cross-boundary references use IDs without DB-level FK enforcement.

### DR-008 Surrogate IDs + Business Keys

Use surrogate technical IDs internally (`UUID`, `bigint`). Use explicit business keys for human/business uniqueness (`employee_id`, `invoice_number`). Never expose surrogate IDs to users when business keys exist.

---

## UI display

### DR-009 Date Display

Use short date format (`YYYY-MM-DD` or locale-short) by default. Show time only when business-significant. Never display ISO-8601 raw timestamps to end users.

### DR-010 System and Audit Field Visibility

Hide system-generated fields (surrogate IDs, audit timestamps, version numbers) by default in lists, details, and forms. Show on request via an explicit reveal control.

### DR-011 Foreign Reference Display

Do not display foreign keys directly to users. Show the related record's business key or business label in lists, details, and forms.

### DR-012 Related Entity Selection — Dropdown

Use a dropdown that displays all related-entity names when the candidate set is small enough for fast visual selection (typically under ~50 items).

### DR-013 Related Entity Selection — Popup Lookup

Use a popup entity lookup with multiple descriptive fields and filtering when users need richer context to choose a related entity (typically ~50 to several hundred items).

### DR-014 Related Entity Selection — Search Required

Require the user to enter search criteria before querying when the candidate set is too large for full preloading (typically thousands or more).

---

## Validation and documentation

### DR-015 Full E2E Test and Coverage

Generate full end-to-end tests using real database connections (not mocks). Provide coverage results as validation evidence. Mocking is allowed for external services that can't be safely run in tests.

### DR-016 Feature User and System Documentation

Every feature produces both:

- `docs/users/<feature>.md` — end-user-facing, with screenshots when realistic
- `docs/system/<feature>.md` — architecture/internals, with diagrams where they materially explain structure, flow, state, or integration

ERD is mandatory when `data-model` is in the spec's impact list. See `preset/IMPACT_ANALYSIS.md` for the full diagram-trigger table.
