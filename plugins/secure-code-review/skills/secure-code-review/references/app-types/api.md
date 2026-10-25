# App type: API / backend

REST, GraphQL, gRPC. Primary taxonomy: OWASP API Security Top 10 2023
(`api-security.md`), plus Top 10:2025 and ASVS V4.

## Entry points

Endpoints/resolvers/methods, request bodies and params, headers (auth, content
negotiation), webhooks, and upstream API responses consumed.

## Top checks

- **BOLA / object authorization** (API1) — every object fetched/mutated by ID is
  scoped to owner + tenant. The highest-yield API check. → role matrix.
- **BFLA / function authorization** (API5) — privileged operations gated by role.
- **BOPLA** (API3) — input binding (mass assignment) and output serialization
  (over-exposure). Check DTOs vs entities, which fields in/out.
- **Resource consumption** (API4) — pagination caps, query cost/complexity limits
  (GraphQL depth/complexity), payload size, rate limits on costly flows.
- **Auth** (API2) — token verification (`auth-session-crypto.md`), key handling.
- **SSRF** (API7); **misconfig / CORS** (API8); **inventory** (API9) — old/debug/
  unversioned endpoints (variant hunt); **unsafe upstream consumption** (API10).

## GraphQL specifics

Introspection in production, missing field-level authz, query depth/alias/batching
abuse, mutations without the same checks as queries.

## gRPC / RPC

Per-method authz, message size limits, TLS/mTLS, reflection exposure.

## Sources

- OWASP API Security Top 10 2023 — https://owasp.org/API-Security/ (CC BY-SA).
- OWASP ASVS 5.0.0 V4 (CC BY-SA).
