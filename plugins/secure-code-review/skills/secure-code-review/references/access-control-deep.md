# Broken access control — deep dive

Driven by the confirmed role matrix. OWASP Top 10:2025 ranks Broken Access
Control **A01** (and folds SSRF in). Check every route/handler/resolver/screen/
function against the matrix.

## The types

- **Vertical escalation** — a lower role reaches a higher role's function
  (user → admin). CWE-862 Missing Authorization, CWE-863 Incorrect Authorization.
- **Horizontal / IDOR / BOLA** — acting on another user's object by changing an
  identifier; no ownership check. CWE-639, OWASP API1:2023.
- **BFLA** — function-level: an admin/privileged endpoint lacks a role check.
  OWASP API5:2023, CWE-862.
- **BOPLA / mass assignment** — field-level: the caller sets properties they
  shouldn't (`role`, `is_admin`, `tenant_id`, `balance`). OWASP API3/API6:2023,
  CWE-915.
- **Missing function-level check** — the guard is simply absent on a sensitive
  handler.
- **Insecure direct object reference / forced browsing** — guessable IDs/paths
  reachable without authorization.
- **JWT / claim tampering** — trusting unverified or client-supplied claims
  (`role`, `isAdmin`), `alg:none`, unverified signature. See `auth-session-crypto.md`.
- **Tenant isolation break** — queries not scoped by `tenant_id`; cross-tenant read/write.
- **SSRF-enabled pivot** — server fetches an attacker-controlled URL and reaches
  internal services/metadata. CWE-918; under A01:2025.
- **CORS misconfig** — reflected origin / `*` with credentials, widening who can
  call authenticated endpoints.
- **TOCTOU on authz** — the check and the privileged use are not atomic.
- **Default-allow / fail-open** — the guard grants access on error or when a
  condition is unmet. CWE-636; see also A10:2025.

## How to check each route

For every entry point in your route enumeration:

1. What role/ownership/tenant does the matrix require?
2. What does the code actually enforce, and where (`file:line`)? Middleware,
   decorator, in-handler check, or nothing?
3. Is the check before the sensitive action, and does it cover ownership **and**
   tenant **and** fields — not just "is logged in"?
4. Can it be bypassed — alternate route to the same handler, verb tampering,
   trusting a client-supplied role/ID, parameter pollution?

For each gap, state: **role expected vs enforced**, the code location of the
missing/incorrect check, and live evidence if an authorized live pass confirmed it.

## Common root causes

- Authorization by convention (URL prefix `/admin`) instead of by check.
- Ownership assumed from the session but the object fetched by raw ID.
- Query filters applied in the UI/list endpoint but not the detail/mutation one.
- New routes added without the guard the old ones had (variant hunt!).

## Sources

- OWASP Top 10:2025 A01 Broken Access Control — https://top10.owasp.org/2025 (CC BY-SA).
- OWASP API Security Top 10 2023 (API1 BOLA, API3 BOPLA, API5 BFLA) (CC BY-SA).
- OWASP ASVS 5.0.0 V8 Authorization (CC BY-SA).
- CWE-862, CWE-863, CWE-639, CWE-915, CWE-918, CWE-284, CWE-636 — cwe.mitre.org.
