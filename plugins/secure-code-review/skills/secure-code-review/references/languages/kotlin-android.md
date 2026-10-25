# Kotlin / Android — sources, sinks, footguns

Android app code. Pair with `app-types/mobile.md` (MASVS/MASTG). JVM injection
rules from `java-kotlin.md` also apply.

## Sources

`Intent` extras, exported components, deep links / `Uri`, `ContentProvider` queries,
`WebView` message handlers, clipboard, network responses, files, `SharedPreferences`.

## Android-specific sinks

| Sink | Risk | MASVS / CWE | Safe equivalent |
| :- | :- | :- | :- |
| exported `Activity`/`Service`/`Receiver`/`Provider` with no permission | IPC exposure | MASVS-PLATFORM / CWE-926 | `exported=false` or signature permission |
| implicit `Intent` with sensitive data | intent interception | MASVS-PLATFORM | explicit intents; verify caller |
| `WebView` `addJavascriptInterface` + `setJavaScriptEnabled` on remote/input | RCE/XSS | CWE-749/79 | avoid JS bridge for untrusted content |
| `WebView` `loadUrl(userUrl)` / file access flags | XSS/file read | CWE-79/22 | validate; disable file access |
| `SharedPreferences`/plain files for secrets | insecure storage | MASVS-STORAGE / CWE-312 | EncryptedSharedPreferences / Keystore |
| SQLite `rawQuery` with concat (Room `@RawQuery`) | SQL inj | CWE-89 | parameterized / Room DAO params |
| `ContentProvider` query building from input | SQL/provider inj | CWE-89 | selection args |

## Storage, transport, crypto (MASVS)

- Secrets in Keystore, not prefs/plist/code (MASVS-STORAGE/CRYPTO). Flag hardcoded
  keys/API tokens in code or resources (CWE-798).
- Cleartext traffic (`usesCleartextTraffic`), missing network-security-config, no
  pinning where required (MASVS-NETWORK).
- Sensitive data in logs/clipboard/screenshots/backups (`allowBackup=true`)
  (MASVS-PRIVACY/STORAGE).

## Footguns

- `PendingIntent` without `FLAG_IMMUTABLE`; mutable intents hijacked.
- Deep-link handlers trusting params without verifying the app link / caller.
- Debuggable release builds; disabled `minifyEnabled` leaving secrets in strings.

## Safe-by-default (do NOT flag)

`exported=false` components, Room DAO bound params, EncryptedSharedPreferences /
Keystore, explicit intents, `FLAG_IMMUTABLE` PendingIntents.

## Sources

- OWASP MASVS v2.1.0 / MASTG — https://mas.owasp.org/ (CC BY-SA).
- Android security guidance — https://developer.android.com/privacy-and-security.
- CWE-926/749/79/312/89/798/22 — cwe.mitre.org.
