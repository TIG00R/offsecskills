# Java / Kotlin — sources, sinks, footguns

JVM server-side (Spring, Jakarta). Android specifics are in
`app-types/mobile.md` and `languages/kotlin-android.md`.

## Sources

`HttpServletRequest` params/headers/cookies, Spring `@RequestParam/@PathVariable/
@RequestBody`, message listeners, file/stream input, JNDI lookups.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `Statement` + string SQL | SQL inj | 89 | `PreparedStatement` with `?`; JPA bound params |
| `Runtime.exec`, `ProcessBuilder` with concatenation | OS cmd inj | 78 | arg array, no shell |
| `ObjectInputStream.readObject` on untrusted | deserialization | 502 | avoid; allowlist via `ObjectInputFilter`; use JSON |
| XML parse without hardening | XXE | 611 | disable DTDs/external entities |
| `el`/SpEL/OGNL/`ScriptEngine` on input | expression/code inj | 94 | don't evaluate input |
| JNDI lookup of input (Log4Shell-style) | injection/RCE | 917 | never resolve input via JNDI |
| `new File(userPath)` | path traversal | 22 | canonicalize + verify base |
| `Math.random()`/`java.util.Random` for tokens | weak RNG | 338 | `SecureRandom` |
| `MessageDigest MD5/SHA1` for passwords | weak hash | 916 | argon2/bcrypt/scrypt |

## Framework footguns

- **Spring** — missing `@PreAuthorize`/method security on a sensitive endpoint;
  `@RequestBody` binding to an entity (mass assignment) — use a DTO + `@Validated`;
  actuator endpoints exposed; permissive CORS; disabled CSRF with cookie sessions.
- **Deserialization** — Jackson polymorphic typing (`enableDefaultTyping`/
  `@JsonTypeInfo`) on untrusted JSON. CWE-502.
- **SSRF** — `RestTemplate`/`WebClient`/`URL.openConnection` on input URL.

## Safe-by-default (do NOT flag)

`PreparedStatement`/JPA bound params, DTOs with validation, `SecureRandom`,
hardened XML parsers, Spring Security with method-level checks present.

## Sources

- OWASP Cheat Sheets: Injection, Deserialization, XXE Prevention (CC BY-SA).
- SEI CERT Oracle Coding Standard for Java (© SEI/CMU).
- CWE-89/78/502/611/917/94/22/338/916 — cwe.mitre.org.
