# Authentication, sessions, and cryptography

Covers OWASP A04/A07:2025, API2:2023, ASVS V6/V7/V9/V10/V11. Where auth and crypto
break in code.

## Authentication (A07, API2, ASVS V6)

- **Password storage** — must be a slow salted hash (argon2id, scrypt, bcrypt).
  Flag MD5/SHA-1/SHA-256-without-KDF, unsalted, or home-grown. CWE-916/327.
- **Credential handling** — no plaintext logging of passwords/tokens; constant-time
  comparison for secrets; anti-automation on login/reset (API6). CWE-307.
- **MFA & recovery** — recovery flow as strong as login; no user enumeration via
  timing or distinct messages.
- **Missing auth** — sensitive function with no authentication at all. CWE-306.

## Sessions (A07, ASVS V7)

- Session ID regenerated on login/privilege change; invalidated on logout; sane
  idle/absolute expiry. CWE-384 session fixation.
- Cookies: `HttpOnly`, `Secure`, `SameSite`; scoped path/domain.

## Tokens — JWT / PASETO (ASVS V9)

- **Verify, don't decode.** `jwt.verify` with a pinned algorithm list — never
  `decode()` for auth, never accept `alg:none`, never allow HS/RS confusion. CWE-347.
- Validate `exp`, `nbf`, `iss`, `aud`; check revocation where required.
- Never trust client-supplied authorization claims (`role`, `isAdmin`) without
  server-side verification. → access control.

## OAuth / OIDC (ASVS V10)

- Authorization-code + PKCE for public clients; exact redirect-URI matching;
  validate `state`; don't leak tokens in URLs/logs; validate ID-token signature
  and audience.

## Cryptography (A04, ASVS V11)

- Approved algorithms only (AES-GCM/ChaCha20-Poly1305; RSA-OAEP/ECDSA/EdDSA; SHA-2/3).
  Flag DES/3DES/RC4/ECB, static IVs, MD5/SHA-1 for security. CWE-327/326.
- **Randomness** — CSPRNG for tokens/keys/IVs (`secrets`, `crypto.randomBytes`,
  `/dev/urandom`), never `rand()`/`Math.random()`/`java.util.Random`. CWE-338.
- **Key & secret management** — no hardcoded keys/passwords/tokens (CWE-798);
  secrets from a vault/env, not source; rotation possible. Redact any secret found
  (first 4 chars + location).
- No home-grown crypto or custom protocols.

## Sources

- OWASP Top 10:2025 A04/A07; API Security Top 10 2023 API2 (CC BY-SA).
- OWASP ASVS 5.0.0 V6/V7/V9/V10/V11 (CC BY-SA).
- OWASP Cheat Sheets: Password Storage, Authentication, Session Management, JWT,
  Cryptographic Storage (CC BY-SA).
- CWE-287/307/384/347/798/327/338/916 — cwe.mitre.org.
