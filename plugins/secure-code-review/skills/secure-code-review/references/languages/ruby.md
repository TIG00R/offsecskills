# Ruby / Rails — sources, sinks, footguns

## Sources

`params`, `request.*`, `cookies`, `session`, `ARGV`, `ENV`, file/stream input.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| string-interpolated SQL in `where`/`find_by_sql` | SQL inj | 89 | `where("x = ?", v)` / hash conditions |
| `system`, `exec`, `` `backticks` ``, `%x{}`, `open("|cmd")` | OS cmd inj | 78 | `system(bin, *args)` array form |
| `eval`, `instance_eval`, `send(user)` | code inj | 94 | avoid; allowlist method names |
| `Marshal.load`, `YAML.load`, `Oj` unsafe | deserialization | 502 | `YAML.safe_load`; JSON |
| `html_safe`, `raw`, `<%== %>` | XSS | 79 | default ERB escaping; `sanitize` |
| `render inline:`/`render params[:x]` | template/file inj | 94/22 | fixed templates |
| `Net::HTTP.get(URI(userUrl))` | SSRF | 918 | allowlist host/scheme |
| `File.open(params[...])` | path traversal | 22 | `File.expand_path` + base check |

## Framework footguns

- **Rails** — mass assignment: `Model.new(params[:x])` without `params.require.permit`
  (strong parameters). CWE-915.
- `send`/`public_send` with a param (method injection); `constantize`/`safe_constantize`
  on input.
- `redirect_to params[:url]` open redirect (CWE-601).
- Skipped `protect_from_forgery` / `skip_before_action :verify_authenticity_token`.
- Dynamic finders / `find_by_sql` with interpolation.

## Safe-by-default (do NOT flag)

ActiveRecord parameterized conditions, strong parameters, ERB auto-escaping,
`YAML.safe_load`.

## Sources

- OWASP Cheat Sheets: Injection, Ruby on Rails Security (CC BY-SA).
- Rails Security Guide — https://guides.rubyonrails.org/security.html.
- CWE-89/78/94/502/79/918/22/915/601 — cwe.mitre.org.
