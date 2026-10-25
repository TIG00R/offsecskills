# App type: mobile (iOS / Android)

Primary taxonomy: OWASP MASVS v2.1.0 (8 categories, 24 controls) + MASTG. Pair with
`languages/swift-objc.md` and `languages/kotlin-android.md`, and `api.md` for the
backend the app talks to.

## MASVS categories (verify at the chosen profile)

| ID | Category | Review focus |
| :- | :- | :- |
| MASVS-STORAGE | Storage of sensitive data | Keychain/Keystore not prefs/plist/files; no secrets in logs/backups |
| MASVS-CRYPTO | Cryptography | strong algorithms, platform crypto, key management, no hardcoded keys |
| MASVS-AUTH | Authentication & authorization | server-side enforcement, biometric done right, session handling |
| MASVS-NETWORK | Network communication | TLS, cert validation/pinning, no cleartext |
| MASVS-PLATFORM | Platform interaction | IPC/exported components, deep links, WebView bridges, permissions |
| MASVS-CODE | Code quality | injection, memory, dependency and update integrity |
| MASVS-RESILIENCE | Reverse-engineering resistance | anti-tamper/root-jailbreak as defense-in-depth, not primary control |
| MASVS-PRIVACY | Privacy | data minimization, consent, no leak via clipboard/screenshots/logs |

## The cardinal rule

Treat the mobile app as **untrusted client code**. All real authorization and
business rules must hold **server-side** — client-side checks are UX, not security.
Review the backend (`api.md`) against the role matrix regardless of client controls.

## High-yield checks

- Secrets/keys hardcoded in the app or resources (extractable). CWE-798.
- Sensitive data in insecure storage, logs, clipboard, screenshots, backups.
- Exported components / deep links acting without verifying caller or params
  (Android); URL-scheme/universal-link handlers (iOS).
- WebView JS bridges to untrusted content.
- Cleartext traffic / disabled TLS validation / missing pinning where required.

## Sources

- OWASP MASVS v2.1.0 — https://mas.owasp.org/MASVS/ (CC BY-SA).
- OWASP MASTG — https://mas.owasp.org/MASTG/ (CC BY-SA).
