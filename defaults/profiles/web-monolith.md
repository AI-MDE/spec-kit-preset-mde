# Profile: web-monolith (default)

Server-rendered web application with REST APIs in a single deployable process. Internal business apps, ops/staff tools, mid-sized SaaS.

This is the mde preset's **default profile**. First-run `specify` uses these defaults unless the user picks an alternative.

---

## Architecture rules (all from `architecture.md`)

- AR-001 Modular Monolith
- AR-002 Layered Application
- AR-003 MVC + REST API
- AR-004 Repository Pattern (where complexity warrants)
- AR-005 Command-Query Separation

## Design rules (all from `design-rules.md`)

- DR-001 through DR-016 (full set)

## Technology stack

See `technology-stack.md` for full defaults. Key choices:

- Frontend: React or server-rendered Pug
- Backend: Node.js + TypeScript
- Database: PostgreSQL
- Auth: app-managed roles, often deferred
- Integration: synchronous HTTP, async when needed

## Profile notes

- One process, one deployment unit.
- Modules within the monolith communicate via direct function calls; bounded-context discipline is internal.
- Cross-cutting concerns (audit, logging, validation) live as shared infrastructure within the same codebase.
- Suitable target for Spec Kit + mde because the surface is uniform — one repo, one stack, one migration story.
