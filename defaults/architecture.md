# Architecture Defaults

Recommended architecture rules (AR-###) the mde preset proposes for new projects. These are *defaults*, not enforced rules. First-run `specify` proposes them in the Application Profile; the user accepts, modifies, or rejects each one. Accepted rules promote to `.specify/memory/app/architecture.md`.

For non-default architectures, see `preset/defaults/profiles/`. Each alternative profile selects which AR rules apply and adds profile-specific guidance.

---

## AR-001 Modular Monolith

Default system style. One deployable application, structured into modules with clear boundaries.

**Adopt when:** the team is small to medium, deployment simplicity matters, the domain doesn't yet justify distributed services.

**Override when:** scale, isolation, or independent deploy cadence justifies splitting into services.

---

## AR-002 Layered Application

Default structural model: UI/API → Application/Service → Domain → Infrastructure. Each layer depends only on layers below it.

**Adopt when:** any non-trivial business application.

**Override when:** the project is a script, CLI tool, or pure utility where layering adds ceremony without value.

---

## AR-003 MVC + REST API

Default interaction model: server-rendered Model-View-Controller for staff/internal UIs, plus REST-style JSON APIs for integrations and progressive enhancement. Same application process serves both.

**Adopt when:** internal business apps with mixed UI and integration needs.

**Override when:** UI is SPA-only, API-only, event-driven, or there's no UI at all.

---

## AR-004 Repository Pattern

Use the Repository pattern when persistence complexity or aggregate access patterns justify the abstraction. Avoid ceremonial repositories for trivial CRUD.

**Adopt when:** the domain has aggregates with non-trivial invariants or multiple persistence backends.

**Override when:** simple table-per-entity CRUD where direct ORM use is clearer.

---

## AR-005 Command-Query Separation

Conceptually separate commands (state-changing) from queries (read-only). Full CQRS (separate read/write models) only when read and write paths materially diverge.

**Adopt when:** any application with non-trivial business operations.

**Override when:** purely CRUD or read-mostly applications where the conceptual separation adds nothing.
