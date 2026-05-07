# Profile: event-driven

System where state changes are captured as events. May use event sourcing (events as source of truth), event-carried state transfer, or pub/sub for asynchronous workflows.

Use this profile when business processes are inherently asynchronous, when audit and replay are first-class requirements, or when read and write models materially diverge.

---

## Architecture rules

Apply (from `architecture.md`):

- AR-001 Modular Monolith — *or* microservices, depending on scale
- AR-002 Layered Application
- AR-005 Command-Query Separation — *full CQRS, not just conceptual*

Adjusted:

- AR-004 Repository Pattern — apply to write side; read side typically uses query-optimized projections, not repositories.

Drop or adjust:

- AR-003 MVC + REST API — commands often go through a command bus rather than CRUD endpoints.

## Design rules

Apply: DR-001, DR-002, DR-005, DR-006, DR-008, DR-015, DR-016, plus most UI rules (DR-009..DR-014) when there's a UI.

Adjusted:

- DR-003 Transaction Boundaries — write-side transaction commits the event(s); projections eventually update.
- DR-004 Optimistic Locking — versioning happens at the aggregate event-stream level.
- DR-007 Referential Integrity — read models may relax FKs in favor of denormalized views.

## Profile-specific additions

- **Events as durable record:** events are append-only and the source of truth for state.
- **Projections (read models):** built from events, optimized per query. Multiple projections may serve different views.
- **Eventual consistency:** UI may show eventually-consistent views; surface this to users when relevant.
- **Replay capability:** projections can be rebuilt from events; saga state can be replayed.

## Technology stack

- Event store: EventStoreDB, Kafka, or PostgreSQL with an events table
- Read store: PostgreSQL, Elasticsearch, or specialized stores per projection
- Backend: Node.js / TypeScript or other strong-typed runtime
- Messaging: Kafka, NATS, or in-process for monolithic event-sourced setups

## Profile notes

Event-driven systems have rich literature (Greg Young, Vaughn Vernon, etc.). The preset surfaces the rule selection; design depth is the project's responsibility. Adopt only when the team understands eventual consistency and event versioning.
