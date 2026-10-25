# Go — sources, sinks, footguns

## Sources

`r.URL.Query()`, `r.FormValue`, `r.Body`, headers/cookies, `os.Args`, `os.Getenv`,
file/stream input, RPC/queue payloads.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `fmt.Sprintf` into SQL + `db.Query` | SQL inj | 89 | `db.Query("… ?", x)` placeholders |
| `exec.Command("sh","-c", s)` | OS cmd inj | 78 | `exec.Command(bin, args...)` no shell |
| `text/template` to HTML | XSS | 79 | `html/template` (context-aware escaping) |
| `os.Open(filepath.Join(base, user))` unverified | path traversal | 22 | `filepath.Clean` + `strings.HasPrefix(base)` |
| `http.Get(userURL)` | SSRF | 918 | allowlist; block internal IPs; custom dialer |
| `encoding/gob`/`json` into `interface{}` then type-assert loosely | type confusion | 843 | fixed structs; validate |
| `math/rand` for tokens | weak RNG | 338 | `crypto/rand` |

## Concurrency (Go's sharp edge)

- Check-then-act on shared state without a `sync.Mutex` / channel / `SELECT … FOR
  UPDATE` → TOCTOU race (CWE-367), the engine of business-logic abuse (BLA1). Look
  for read → decide → write on a package-level var or shared struct.
- `go func(){…}` capturing a loop variable (pre-1.22) or shared map without lock
  (`fatal error: concurrent map writes`).
- Unchecked errors hiding security failures (`_ =` on an authz/verify call).

## Footguns

- `errors`/`defer` swallowing an authz failure then proceeding (fail-open, A10).
- Missing context timeouts on outbound requests (resource exhaustion).
- `html/template` is safe; `text/template` into HTML is not — flag the latter.

## Safe-by-default (do NOT flag)

`database/sql` placeholders, `html/template`, `crypto/rand`, `exec.Command` with
separate args, mutex-guarded shared state.

## Sources

- OWASP Cheat Sheets: Injection, SSRF (CC BY-SA).
- CWE-89/78/79/22/918/367/843/338 — cwe.mitre.org.
