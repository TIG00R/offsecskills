# JavaScript / TypeScript — sources, sinks, footguns

Node (server) and browser. TypeScript types do not stop injection — runtime input
is still untrusted.

## Sources

`req.query/body/params/headers/cookies`, `process.argv/env`, `location.*`,
`document.*`, `postMessage`, `fetch`/response bodies, websocket messages, file reads.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| string-built SQL (`` `… ${x}` ``) | SQL inj | 89 | parameterized query / query builder bound params |
| `child_process.exec`, `execSync` | OS cmd inj | 78 | `execFile`/`spawn` with arg array |
| `eval`, `new Function`, `vm` on input | code inj | 94 | don't |
| `innerHTML`, `dangerouslySetInnerHTML`, `document.write` | DOM XSS | 79 | textContent; framework escaping; sanitize (DOMPurify) |
| server template of raw input | SSTI/XSS | 94/79 | escape; fixed templates |
| `res.send('<h1>'+x)` | reflected XSS | 79 | escape; set correct content-type |
| `fetch(userUrl)` server-side | SSRF | 918 | allowlist host/scheme; block metadata IPs |
| `JSON`/`yaml`/`serialize` of untrusted + `eval`-y revivers | injection | 502 | plain JSON.parse; no function revivers |
| `fs.readFile(userPath)` | path traversal | 22 | `path.resolve` + prefix check |
| `Math.random()` for tokens | weak RNG | 338 | `crypto.randomBytes`/`webcrypto` |
| `jwt.decode()` for auth; `alg:['none']` | broken token | 347 | `jwt.verify` with pinned alg |

## Framework footguns

- **Express** — missing authz middleware on a route; `cors()` reflecting origin with
  credentials; `Object.assign(model, req.body)` mass assignment; trusting
  `x-forwarded-*`.
- **React/Vue/Angular** — `dangerouslySetInnerHTML`, `v-html`, `bypassSecurityTrust*`;
  building URLs with `javascript:`.
- **Prototype pollution** — merging untrusted objects (`lodash.merge`, `__proto__`).
  CWE-1321.
- **npm** — `postinstall` scripts, typosquats, `latest`/unpinned ranges.

## Safe-by-default (do NOT flag)

React/JSX text interpolation, parameterized queries, `execFile` with args,
`crypto.randomBytes`, `jwt.verify` with pinned algorithms.

## Sources

- OWASP Cheat Sheets: XSS Prevention, DOM-based XSS, Node.js Security, SSRF
  (CC BY-SA).
- CWE-79/89/78/94/918/347/1321/338 — cwe.mitre.org.
