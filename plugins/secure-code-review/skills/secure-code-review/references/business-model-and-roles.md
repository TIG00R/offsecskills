# Business model and the role matrix

Derive what the app does and who may do what, then **confirm it with the user**
before hunting access-control flaws. The confirmed matrix is ground truth.

## Derive the model

From code, config, routes, README, and docs, establish:

- **Core functions** — the features and the operations each exposes.
- **Data model & sensitive assets** — entities, which hold PII/money/secrets,
  ownership fields (`owner_id`, `user_id`), tenancy fields (`tenant_id`, `org_id`).
- **Actors / roles** — anonymous, user, premium, admin, support, service account,
  tenant, system. Include machine/service identities.
- **Authentication mechanism** — sessions, JWT, OAuth/OIDC, API keys, mTLS.
- **Trust boundaries** — where data crosses from less-trusted to more-trusted
  (client→server, service→service, tenant→tenant, user→admin).

Enumerate each route/handler by grepping the framework's route registrations
(decorators, router calls, mapping annotations), and note the guard currently on
it — the gap between the guard present and the guard required is the finding set.

## Build the role matrix

A table of **role × resource/action** marking who is allowed to do what. Capture
three dimensions access-control bugs hide in:

- **Object ownership** — may role X act on another user's object? (horizontal /
  IDOR / BOLA)
- **Tenant isolation** — may any role read/write another tenant's data?
- **Field-level** — which fields may a role set or read? (mass assignment / BOPLA;
  e.g. `role`, `is_admin`, `balance` are admin-only)

Example shape (see `evals/fixtures/role-matrix.md` for a full one):

| Resource / action | anonymous | user | admin |
| :- | :- | :- | :- |
| GET /orders/{id} | deny | own + same tenant | same tenant |
| PATCH /users/{id} | deny | self; fields name,email | any field |
| DELETE /admin/users/{id} | deny | deny | same tenant |

## The confirmation gate (hard stop)

Present the derived **purpose + role matrix** to the user and stop:

> "Confirm or correct this before I hunt for access-control flaws."

Do not emit access-control findings until the user confirms. If they correct it,
use the corrected version. This gate exists because almost every access-control
finding is "code allows X; policy forbids X" — without the confirmed policy, you
are guessing at intent and will produce false positives or miss real gaps.

## Hand-off

The confirmed matrix drives `access-control-deep.md` (every route checked against
it) and informs `business-logic-abuse.md` (which transitions require which role).

## Sources

- OWASP API Security Top 10 2023 — object/function/property-level authorization
  (CC BY-SA).
- OWASP ASVS 5.0.0 — V8 Authorization (CC BY-SA).
- OWASP Code Review Guide v2 — transaction/role analysis (CC BY-SA).
