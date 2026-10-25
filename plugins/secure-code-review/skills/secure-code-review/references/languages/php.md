# PHP — sources, sinks, footguns

## Sources

`$_GET/$_POST/$_REQUEST/$_COOKIE/$_SERVER`, `php://input`, `$_FILES`, `getenv`,
file/stream input.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `mysqli_query`/`$pdo->query` with concatenation | SQL inj | 89 | PDO prepared statements with bound params |
| `exec`, `shell_exec`, `system`, `` `backticks` ``, `popen` | OS cmd inj | 78 | avoid; `escapeshellarg` + fixed command |
| `eval`, `assert`, `create_function`, `preg_replace` `/e` | code inj | 94 | don't |
| `unserialize($_…)` | object injection | 502 | JSON; `unserialize(x,['allowed_classes'=>false])` |
| `include/require $user` | LFI/RFI | 98 | fixed includes; allowlist |
| `echo $_GET[...]` into HTML | XSS | 79 | `htmlspecialchars`; templating auto-escape |
| `file_get_contents($userUrl)`, cURL to input URL | SSRF | 918 | allowlist host/scheme |
| `fopen($userPath)` | path traversal | 22 | `realpath` + base check; basename |
| `md5/sha1` for passwords | weak hash | 916 | `password_hash` (bcrypt/argon2) |

## Framework footguns

- **Laravel** — `DB::raw`/`whereRaw` with input; `Model::create($request->all())`
  mass assignment (set `$fillable`/`$guarded`); `{!! !!}` Blade raw output;
  `APP_DEBUG=true` in prod.
- **Symfony** — bypassing Twig autoescape with `|raw`; unvalidated form mapping.
- `extract($_REQUEST)` / `$$var` variable-variables.

## Safe-by-default (do NOT flag)

PDO prepared statements, Blade/Twig auto-escaping, `password_hash`/`password_verify`,
`unserialize` with `allowed_classes=>false`.

## Sources

- OWASP Cheat Sheets: Injection, Deserialization, PHP Configuration (CC BY-SA).
- CWE-89/78/94/502/98/79/918/22/916 — cwe.mitre.org.
