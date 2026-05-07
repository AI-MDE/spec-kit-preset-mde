# Technology Stack Defaults

Recommended technology categories the mde preset proposes for new projects. These are *defaults* aligned with the `web-monolith` profile. First-run `specify` proposes them; the user confirms, overrides, or defers each one. Accepted choices promote to `.specify/memory/app/technology-stack.md`.

Each entry carries a status: `proposed`, `confirmed`, `deferred`, or `rejected`.

---

## Frontend

| Choice | Status |
|---|---|
| React (SPA or hybrid) | proposed |
| Server-rendered Pug or equivalent (SSR-first) | proposed alternative |

Pick one based on whether the primary UI is interactive client-side or operations-staff server-rendered.

## Backend

| Choice | Status |
|---|---|
| Node.js + TypeScript | proposed |

Default unless a stronger runtime constraint already exists (e.g., team expertise in another stack).

## Database

| Choice | Status |
|---|---|
| PostgreSQL | proposed |

Default for transactional business data. Override only when domain genuinely needs a non-relational store.

## Authentication

| Choice | Status |
|---|---|
| App-managed roles + token/session auth | proposed |
| External identity provider (OAuth, OIDC, SAML) | proposed alternative |

Often `deferred` at first-run; concrete choice surfaces when a feature spec needs it.

## Integration / Messaging

| Choice | Status |
|---|---|
| Synchronous HTTP first | proposed |
| Add async messaging when boundaries or workflows require it | proposed extension |

Start synchronous; introduce a message bus only when a real workflow needs it.

## Testing Stack

| Choice | Status |
|---|---|
| Vitest / Jest for unit and integration | proposed |
| Supertest for HTTP integration | proposed |
| Playwright or equivalent for full E2E (per DR-015) | proposed |

## Deployment / Runtime

| Choice | Status |
|---|---|
| Container-friendly (Docker + Linux host) | proposed |

Override when an existing platform constraint dictates otherwise.

## Dev Tooling

| Choice | Status |
|---|---|
| TypeScript strict mode | proposed |
| ESLint + Prettier | proposed |
| Spec Kit + mde preset | confirmed |
