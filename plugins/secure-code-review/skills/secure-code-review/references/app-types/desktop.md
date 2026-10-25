# App type: desktop

Native (Win32/macOS/Linux) and cross-platform (Electron, Tauri, Qt, .NET, Java).
Trust model: code runs on the user's machine, but still processes untrusted files,
URLs, IPC, and update feeds.

## Entry points

Opened files/documents, custom URL schemes / protocol handlers, command-line args,
IPC/named pipes/sockets, auto-update feeds, loaded plugins/extensions, clipboard,
drag-and-drop.

## Top checks

- **Untrusted file/URL parsing** — the main attack surface; parser memory safety
  (`c-cpp.md`), deserialization (CWE-502), path traversal on extract/open.
- **Auto-update integrity** (A08) — signed updates, verified over TLS, no downgrade;
  no unsigned code load. CWE-494.
- **Local privilege / secrets** — secrets in plaintext config or world-readable
  files; over-broad file permissions; running as admin unnecessarily.
- **IPC / protocol handlers** — validate caller and arguments; a registered URL
  scheme can be invoked by any website.
- **Supply chain** — bundled dependencies, plugin loading from untrusted paths
  (DLL/dylib search-order hijack).

## Electron specifics

- `nodeIntegration: true` with remote/untrusted content → RCE. Require
  `contextIsolation: true`, `sandbox: true`, a minimal `preload` bridge.
- `webSecurity: false`, loading remote URLs into a privileged window,
  `shell.openExternal(userInput)`, `child_process` reachable from the renderer.
- Validate IPC channel messages; don't expose Node APIs wholesale over the bridge.

## Tauri / others

Command allowlist scope, filesystem scope, and the JS↔native boundary.

## Sources

- OWASP Cheat Sheets: Deserialization, OS Command Injection (CC BY-SA).
- Electron Security checklist — https://www.electronjs.org/docs/latest/tutorial/security.
- CWE-502/494/22/427 — cwe.mitre.org.
