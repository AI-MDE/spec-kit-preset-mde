# Profile: api-only

Backend service exposing REST or gRPC APIs. No UI in the same codebase. Frontend (if any) lives in a separate repository.

Use this profile for headless backends: integration platforms, mobile/SPA backends, partner APIs, internal microservice endpoints (when not using the full microservices profile).

---

## Architecture rules

Apply (from `architecture.md`):

- AR-001 Modular Monolith — *or* microservices for larger systems
- AR-002 Layered Application
- AR-004 Repository Pattern (where complexity warrants)
- AR-005 Command-Query Separation

Drop:

- AR-003 MVC + REST API — there's no MVC, just REST/gRPC. The API style is REST or gRPC, not server-rendered.

## Design rules

Apply: DR-001..DR-008 (persistence and consistency, all relevant), DR-015 (E2E test), DR-016 (system docs are critical for API-only).

Drop (UI display rules don't apply):

- DR-009..DR-014 — no end-user UI in this codebase.

User documentation in DR-016 means **API documentation** (OpenAPI/AsyncAPI specs, integration guides, sample requests), not screenshots.

## Profile-specific additions

- **API contract first:** define the API contract (OpenAPI, Protobuf, GraphQL schema) before implementation. Contracts are first-class artifacts in `contracts/api/`.
- **Versioning:** plan API versioning from day one (URL versioning, header versioning, or semantic versioning of contracts).
- **Authentication/authorization:** typically token-based (JWT, OAuth) since there's no session UI.
- **Rate limiting and observability:** essential for any externally-exposed API.

## Technology stack

- Backend: Node.js + TypeScript, Go, Python, or whatever fits the team
- API framework: Express/Fastify (Node), Gin (Go), FastAPI (Python)
- Database: PostgreSQL by default
- Auth: JWT/OAuth, no session/cookie patterns
- Documentation: OpenAPI / Swagger UI auto-generated from contracts

## Profile notes

API-only is the most common backend shape and the easiest to scope. The preset's defaults work directly with minimal adjustment beyond dropping UI display rules.
