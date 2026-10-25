# OWASP Top 10:2025

The 2025 list. Per category: what it is, code smells, a CWE anchor, and the cheat
sheet to cite for the fix. Cite the Axx ID on each finding where it applies.

> As of writing, the 2025 list is published with minor editorial updates still
> possible; the ten categories and order below are final. Confirm wording at
> https://top10.owasp.org/2025 if quoting a title verbatim.

## The categories

- **A01 Broken Access Control** — authz gaps; **SSRF is folded in here in 2025**.
  Smells: missing/428 role checks, IDOR, mass assignment, server fetch of
  user-supplied URL. CWE-862/863/639/918. → `access-control-deep.md`,
  Authorization Cheat Sheet.
- **A02 Security Misconfiguration** — insecure defaults, verbose errors, open
  cloud buckets, debug on, permissive CORS. CWE-16/732. → `supply-chain-iac-ci.md`.
- **A03 Software Supply Chain Failures** — expanded from "vulnerable components"
  to the whole dependency/build/distribution chain: unpinned deps, typosquats,
  compromised CI, unverified artifacts. CWE-1104/1357. → `supply-chain-iac-ci.md`.
- **A04 Cryptographic Failures** — weak/again-used crypto, plaintext secrets,
  bad key management, no TLS. CWE-327/798/311. → `auth-session-crypto.md`.
- **A05 Injection** — SQL/NoSQL/OS/LDAP/XPath/template, and XSS. Untrusted data
  reaches an interpreter/sink without parameterization or encoding. CWE-89/78/79/94.
  → `languages/*`, Injection & Query Parameterization Cheat Sheets.
- **A06 Insecure Design** — the flaw is in the design, not a bug: missing threat
  model, no rate limit on a sensitive flow, trust where there should be none.
  CWE-657. → `business-logic-abuse.md`.
- **A07 Authentication Failures** — weak auth, credential stuffing, broken
  session/MFA, JWT verification flaws. CWE-287/384/347. → `auth-session-crypto.md`.
- **A08 Software or Data Integrity Failures** — unverified updates/plugins,
  insecure deserialization, unsigned artifacts, CI/CD tampering. CWE-502/345/494.
- **A09 Security Logging & Alerting Failures** — security events not logged,
  logs with secrets/PII, no alerting on abuse. CWE-778/532/117.
- **A10 Mishandling of Exceptional Conditions** — new in 2025: improper error
  handling, logic errors on edge cases, **failing open**, abnormal-condition
  handling that grants access or leaks state. CWE-755/636/388.

## Using this file

Map each confirmed finding to its Axx and the precise CWE from
`cwe-top25-2025.md`. Link the specific OWASP Cheat Sheet for the fix rather than
generic advice. SSRF → A01 (not a standalone 2025 category). XSS and the various
injections → A05.

## Sources

- OWASP Top 10:2025 — https://top10.owasp.org/2025 (CC BY-SA).
- OWASP Cheat Sheet Series — https://cheatsheetseries.owasp.org/ (CC BY-SA).
- CWE — https://cwe.mitre.org/.
