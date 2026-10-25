# OWASP API Security Top 10 (2023)

For any API/backend. The 2023 edition is current. Cite the APIx ID on API
findings alongside the OWASP-2025 and CWE IDs.

## The categories

- **API1 Broken Object Level Authorization (BOLA)** — object accessed by ID with
  no ownership/tenant check. The most common API flaw. CWE-639. → role matrix.
- **API2 Broken Authentication** — weak/missing auth, token flaws, credential
  stuffing. CWE-287/307/347. → `auth-session-crypto.md`.
- **API3 Broken Object Property Level Authorization (BOPLA)** — merges excessive
  data exposure + mass assignment: returning or accepting fields a role shouldn't.
  CWE-915/213.
- **API4 Unrestricted Resource Consumption** — no limits on cost/size/rate;
  expensive queries, large payloads, fan-out. CWE-770/400.
- **API5 Broken Function Level Authorization (BFLA)** — privileged function
  reachable by a lower role; admin endpoints without a role check. CWE-862.
- **API6 Unrestricted Access to Sensitive Business Flows** — a flow (purchase,
  signup, comment) automatable for abuse without anti-automation. → logic abuse.
- **API7 Server-Side Request Forgery (SSRF)** — API fetches a client-supplied URL
  reaching internal resources. CWE-918.
- **API8 Security Misconfiguration** — permissive CORS, missing hardening, verbose
  errors. CWE-16.
- **API9 Improper Inventory Management** — undocumented/old/debug endpoints,
  unversioned APIs, shadow/zombie routes. → variant hunt for `/v1` vs `/internal`.
- **API10 Unsafe Consumption of APIs** — trusting third-party/upstream API data
  without validation. CWE-20.

## What to check

- Every object-returning or object-mutating endpoint: ownership + tenant scope
  (API1/API3) against the confirmed matrix.
- Every privileged endpoint: function-level role check (API5).
- Serializers/DTOs: which fields are bound on input and exposed on output (API3) —
  look for `**request.json`, `Object.assign`, model binders, auto-mapping.
- Rate/size/complexity limits on costly or sensitive flows (API4/API6).
- Any server-side fetch of a caller-influenced URL (API7/SSRF).
- Route inventory for forgotten/debug/old-version endpoints (API9).

## Sources

- OWASP API Security Top 10 2023 — https://owasp.org/API-Security/ (CC BY-SA).
- CWE — https://cwe.mitre.org/.
