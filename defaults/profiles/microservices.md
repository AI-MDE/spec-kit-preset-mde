# Profile: microservices

Service-oriented system. Multiple independently deployable services, each owning its bounded context and persistence.

Use this profile when scale, team independence, or fault isolation justifies the operational cost of distributed services.

---

## Architecture rules

Apply (from `architecture.md`):

- AR-002 Layered Application — *applied within each service*
- AR-004 Repository Pattern — *applied within each service*
- AR-005 Command-Query Separation

Drop (do not apply):

- AR-001 Modular Monolith — replaced by service-oriented topology
- AR-003 MVC + REST API — services are typically API-only; UI lives in a separate frontend

## Design rules

Apply: DR-001..DR-006, DR-008, DR-009, DR-010, DR-011, DR-015, DR-016.

Adjusted:

- DR-007 Referential Integrity — enforce FKs only within each service's owned persistence boundary. Cross-service references are by ID, no DB FK.
- DR-012..DR-014 Related Entity Selection — applies in the frontend service, not in backend services.

## Profile-specific additions

- **Bounded contexts:** each service owns its data and exposes a contract. No shared databases.
- **Async messaging:** introduce a message bus (events) when workflows cross service boundaries. Synchronous HTTP only for query and immediate-response cases.
- **Eventual consistency:** cross-service operations use saga or choreography patterns; transactional consistency is service-local.
- **Observability:** distributed tracing, correlation IDs across services, centralized log aggregation.

## Technology stack

- Frontend: separate SPA service (React or equivalent)
- Backend services: polyglot allowed (Node.js, Go, Python depending on workload)
- Persistence: per-service, polyglot allowed (PostgreSQL, MongoDB, Redis)
- Messaging: Kafka, NATS, or equivalent
- Deployment: container orchestration (Kubernetes or equivalent)

## Profile notes

This profile is intentionally lighter than `web-monolith`. Microservices design has rich literature; the preset's job is to surface the rule set, not re-derive the patterns. Adopt this profile only when the organization has the operational maturity to run distributed services.
