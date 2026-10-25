# Swift / Objective-C — sources, sinks, footguns

Apple platforms. Pair with `app-types/mobile.md` (MASVS) for the full mobile view.

## Sources

URL-scheme / universal-link params, `UIPasteboard`, network responses,
`UserDefaults`, files, IPC/extensions, `WKScriptMessageHandler` messages.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| string-built SQLite (FMDB `executeQuery` with interpolation) | SQL inj | 89 | parameterized `?` binds |
| `WKWebView.evaluateJavaScript` / `loadHTMLString` with input | XSS/JS inj | 79 | escape; avoid injecting input |
| `NSExpression`, `Runtime`/`NSSelectorFromString(user)` | code/selector inj | 94 | allowlist |
| `UnsafePointer`, `memcpy`, C interop | memory | 787/125 | bounds-checked Swift types |
| `Process`/`system` (macOS) with shell | OS cmd inj | 78 | arg array, no shell |
| `URLSession` to input URL | SSRF (if server-side logic) | 918 | validate host/scheme |

## Storage & transport (MASVS-STORAGE / -NETWORK / -CRYPTO)

- Secrets/tokens in `UserDefaults`, plist, or plain files instead of Keychain.
  MASVS-STORAGE-1. Flag Keychain items with weak accessibility
  (`kSecAttrAccessibleAlways`).
- Disabled ATS (`NSAllowsArbitraryLoads`), no cert pinning where required.
  MASVS-NETWORK.
- Home-grown crypto, hardcoded keys, `arc4random` misuse; prefer CryptoKit.
  MASVS-CRYPTO.
- Sensitive data in logs, pasteboard, screenshots, or backups. MASVS-PRIVACY/STORAGE.

## Footguns

- `WKWebView` with `allowFileAccessFromFileURLs`/`javaScriptEnabled` plus input.
- Deep-link / universal-link handlers acting without validating the caller/params.
- Jailbreak/anti-tamper only (MASVS-RESILIENCE) treated as a primary control — it's
  defense in depth, not a substitute.

## Safe-by-default (do NOT flag)

Keychain with appropriate accessibility, parameterized SQLite, CryptoKit, ATS on.

## Sources

- OWASP MASVS v2.1.0 / MASTG — https://mas.owasp.org/ (CC BY-SA).
- CWE-89/79/94/787/125/78/918 — cwe.mitre.org.
