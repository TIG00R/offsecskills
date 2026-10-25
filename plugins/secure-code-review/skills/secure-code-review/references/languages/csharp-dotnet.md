# C# / .NET — sources, sinks, footguns

## Sources

`Request.Query/Form/Headers/Cookies/RouteValues`, model binding, `args`,
`Environment`, file/stream input, message handlers.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `SqlCommand` with string concat / `string.Format` | SQL inj | 89 | parameters (`cmd.Parameters.Add`); EF LINQ / `FromSqlInterpolated` |
| `Process.Start` with a shell/args from input | OS cmd inj | 78 | `ProcessStartInfo` with `ArgumentList`, no shell |
| `BinaryFormatter`, `NetDataContractSerializer`, `LosFormatter` | deserialization | 502 | remove `BinaryFormatter`; `System.Text.Json`/DataContract with known types |
| `XmlDocument`/`XmlReader` default + DTD | XXE | 611 | `DtdProcessing = Prohibit`, null resolver |
| `@Html.Raw`, `MvcHtmlString`, building HTML | XSS | 79 | Razor auto-encoding; encode explicitly |
| `Path.Combine(base, user)` unverified | path traversal | 22 | `Path.GetFullPath` + base-prefix check |
| `HttpClient.GetAsync(userUrl)` | SSRF | 918 | allowlist; block internal ranges |
| `Random` for tokens | weak RNG | 338 | `RandomNumberGenerator` |

## Framework footguns

- **ASP.NET (MVC/Web API)** — over-posting / mass assignment: binding straight to
  entities; use a view model or `[Bind]`/`[BindNever]`. CWE-915.
- Missing `[Authorize]` / policy on a controller or action; `[AllowAnonymous`
  leaking onto sensitive actions.
- Disabled request validation / `ValidateAntiForgeryToken` missing on POST with
  cookie auth.
- EF raw SQL via `FromSqlRaw` with interpolation (use `FromSqlInterpolated` or
  parameters).

## Safe-by-default (do NOT flag)

Parameterized ADO.NET / EF LINQ, Razor auto-encoding, `System.Text.Json`,
`RandomNumberGenerator`, controllers with `[Authorize]` present.

## Sources

- OWASP Cheat Sheets: Injection, Deserialization, XXE, DotNet Security (CC BY-SA).
- Microsoft: `BinaryFormatter` security guidance.
- CWE-89/78/502/611/79/22/918/915/338 — cwe.mitre.org.
