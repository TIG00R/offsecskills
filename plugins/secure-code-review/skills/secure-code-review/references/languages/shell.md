# Shell (bash/sh/zsh) — sources, sinks, footguns

Scripts, entrypoints, CI steps, Dockerfiles. Small scripts cause big problems when
they run privileged or in CI.

## Sources

`$1..$@`, `$ENV`, `read`, command substitution of external data, file contents,
CI context (`$GITHUB_*`, PR titles/branches), webhook payloads.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `eval "$x"` | command injection | 78/94 | avoid eval; arrays for args |
| unquoted `$var` in commands | word-splitting / injection | 78/88 | quote: `"$var"`; arrays `"${arr[@]}"` |
| `curl … | sh` / `wget -O- | bash` | remote code exec | 494 | download, verify hash, then run |
| `rm -rf "$DIR/"` with empty `$DIR` | destructive | 77 | `set -u`; guard empties; `:?` |
| building paths from input | path traversal | 22 | validate; `--` to end options |
| secrets in `echo`/`set -x`/logs | disclosure | 532 | mask; avoid `set -x` around secrets |
| `$(…)` on untrusted then executed | injection | 78 | treat as data; validate |

## Hardening to expect

- `set -euo pipefail` at the top; `IFS` sane.
- Quote every expansion; prefer `[[ ]]`; arrays for argument lists.
- `shellcheck` clean (run `shellcheck` as a lead).

## CI-specific

- Untrusted PR input (title/branch/body) interpolated into a `run:` step → injection
  into the runner. Never interpolate `${{ github.event.* }}` into shell; pass via env
  and quote.
- Secrets printed, or exposed to `pull_request_target` with untrusted checkout.

## Safe-by-default (do NOT flag)

Quoted expansions, array argument lists, `set -euo pipefail`, downloads verified by
checksum before execution.

## Sources

- OWASP Cheat Sheet: OS Command Injection Defense (CC BY-SA).
- ShellCheck wiki — https://www.shellcheck.net/wiki/.
- CWE-78/88/94/494/532/22 — cwe.mitre.org.
