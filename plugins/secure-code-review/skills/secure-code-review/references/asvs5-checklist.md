# OWASP ASVS 5.0.0 checklist

The Application Security Verification Standard, the control checklist to verify
against. 17 chapters, ~350 requirements, three levels (L1 baseline, L2 standard —
**default**, L3 high assurance). Cite the chapter (and requirement ID when you
have it from source) in findings.

## Chapters (V1–V17)

| V | Chapter | What to verify / grep for |
| :- | :- | :- |
| V1 | Encoding and Sanitization | output encoding, context-aware escaping, injection defense at the sink |
| V2 | Validation and Business Logic | positive input validation, business-rule & sequence enforcement |
| V3 | Web Frontend Security | CSP, headers, CSRF defense, clickjacking, DOM sinks |
| V4 | API and Web Service | REST/GraphQL authz, content types, method safety, schema validation |
| V5 | File Handling | upload type/size, path traversal, safe storage/serving |
| V6 | Authentication | credential storage, MFA, anti-automation, recovery flows |
| V7 | Session Management | session issuance/rotation/expiry, cookie flags |
| V8 | Authorization | function/object/field-level checks; the role matrix |
| V9 | Self-contained Tokens | JWT/PASETO verification, claims, `alg`, expiry |
| V10 | OAuth and OIDC | flows, PKCE, redirect URIs, token handling |
| V11 | Cryptography | algorithms, key management, randomness, no homegrown crypto |
| V12 | Secure Communication | TLS config, cert validation, HSTS |
| V13 | Configuration | secure defaults, secrets handling, hardening |
| V14 | Data Protection | PII handling, caching, data minimization |
| V15 | Secure Coding and Architecture | memory safety, dependency trust, safe defaults |
| V16 | Security Logging and Error Handling | log security events, no secrets in logs, safe errors |
| V17 | WebRTC | signaling, media, TURN auth (only if WebRTC is used) |

## How to use

- Pick the level from the interview (default L2). Verify that level's requirements
  for the chapters relevant to the app type; skip chapters that don't apply (V17
  with no WebRTC, V10 with no OAuth).
- ASVS maps closely to the rest: V8 ↔ access-control sweep, V6/V7/V9/V10 ↔
  `auth-session-crypto.md`, V1/V5 ↔ injection and file handling.
- For exact requirement IDs and text, read the official 5.0.0 standard (do not
  invent IDs). Cite the chapter when the specific requirement ID isn't at hand.

## Sources

- OWASP ASVS 5.0.0 — https://owasp.org/ASVS/ and
  https://github.com/OWASP/ASVS (CC BY-SA). Chapter list taken from the 5.0.0
  release.
