# Python — sources, sinks, footguns

## Sources (attacker-controlled)

`request.*` (Flask/Django/FastAPI), `sys.argv`, `os.environ`, `input()`, file
contents, socket/HTTP responses, message payloads, deserialized data.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `cursor.execute(f"… {x}")` / `%`/`+` SQL | SQL injection | 89 | parameterized `execute(q, (x,))`; ORM bound params |
| `os.system`, `subprocess(..., shell=True)` | OS command inj | 78 | `subprocess.run([...], shell=False)` |
| `eval`, `exec`, `compile` | code inj | 94 | don't; `ast.literal_eval` for data |
| `pickle.loads`, `yaml.load`, `marshal` | deserialization | 502 | `yaml.safe_load`; JSON; never pickle untrusted |
| `jinja2.Template(user)` / `render_template_string` | SSTI | 94 | render fixed templates; pass data as context |
| `open(user_path)`, `send_file(user)` | path traversal | 22 | resolve + verify under a base dir |
| `requests.get(user_url)` | SSRF | 918 | allowlist host/scheme; block internal ranges |
| `hashlib.md5/sha1` for passwords | weak hash | 916 | argon2/bcrypt/scrypt |
| `random` for tokens | weak RNG | 338 | `secrets` |

## Framework footguns

- **Django** — `.raw()`/`.extra()` with f-strings; `mark_safe`/`|safe`; `DEBUG=True`
  in prod; `.objects.filter(**request.GET)` mass assignment; `@csrf_exempt`.
- **Flask** — `render_template_string` on input (SSTI); `debug=True`; building SQL
  by hand; missing `SECRET_KEY` hygiene.
- **FastAPI/Pydantic** — overly broad models that bind extra fields; `response_model`
  leaking internal fields.

## Safe-by-default (do NOT flag when used normally)

Jinja autoescape (HTML), ORM querysets with bound params, `yaml.safe_load`,
`secrets`, `subprocess` with a list and `shell=False`.

## Sources

- OWASP Cheat Sheets: Injection Prevention, Deserialization, SSRF (CC BY-SA).
- CWE-89/78/94/502/22/918/916/338 — cwe.mitre.org.
