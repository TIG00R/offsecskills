# App type: web

Server-rendered or SPA + backend. Primary taxonomies: OWASP Top 10:2025, ASVS 5.0
(V3 Web Frontend), relevant CWEs. API backends: also `api.md`.

## Entry points

HTTP routes, form posts, file uploads, websockets, OAuth callbacks, webhooks, and
all client-side input reaching the DOM.

## Top checks

- **Access control** (A01) against the confirmed matrix — every route; IDOR, BFLA,
  forced browsing. → `access-control-deep.md`.
- **Injection** (A05) — SQL/NoSQL/OS/template; **XSS** (reflected, stored, DOM).
  → the language file + Cheat Sheets.
- **CSRF** (CWE-352) — state-changing requests with cookie auth need anti-CSRF or
  SameSite; SPAs using bearer tokens are less exposed — verify which.
- **Auth/session** (A07) — `auth-session-crypto.md`; cookie flags, session fixation.
- **SSRF** (A01) — any server fetch of a user URL.
- **Security headers / config** (A02) — CSP, HSTS, X-Content-Type-Options,
  frame-ancestors; CORS not reflecting origin with credentials.
- **File upload** (CWE-434) — type/size/exec checks, safe storage, no path control.
- **Open redirect** (CWE-601), clickjacking.
- **Logging** (A09) — security events logged, no secrets/PII in logs.

## Client-side specifics

DOM XSS sinks (`innerHTML`, `dangerouslySetInnerHTML`, `v-html`), `postMessage`
origin checks, secrets shipped to the browser, `localStorage` for tokens (XSS
exfil risk), prototype pollution.

## Sources

- OWASP Top 10:2025 (CC BY-SA); OWASP ASVS 5.0.0 V3 (CC BY-SA).
- OWASP Cheat Sheets: XSS, CSRF, Content Security Policy, Clickjacking (CC BY-SA).
