# App type: CLI / tooling

Command-line tools, scripts, build/dev tooling. Often run in CI or with elevated
privilege, so injection and path bugs have high blast radius.

## Entry points

Arguments and flags, stdin, environment variables, config files, files/paths
operated on, network fetches, subprocess output consumed.

## Top checks

- **Command injection** (CWE-78) — building shell strings from args/env/file
  content; see `languages/shell.md`. Prefer argument arrays, no `shell=True`.
- **Argument injection** (CWE-88) — untrusted value used as a flag (e.g. a filename
  starting with `-`); use `--` to end options; validate.
- **Path traversal** (CWE-22) — output/extract/read paths from input; archive
  extraction ("zip slip"); symlink following.
- **Secrets** — tokens in argv (visible in process list), in env dumped to logs, or
  written world-readable. Redact in output and logs (A09).
- **Deserialization / config parsing** (CWE-502) — `pickle`/`yaml.load` of config;
  trusting a project-local config that runs code.
- **Untrusted downloads** — verify integrity before executing; no curl-pipe-to-shell.
- **Privilege** — does it need the privilege it runs with; TOCTOU on files it
  operates on as root (CWE-367).

## Running in CI

Interpolating untrusted CI context (PR title/branch) into a shell step is injection
into the runner; pass via env and quote. Scope tokens/permissions minimally.

## Sources

- OWASP Cheat Sheet: OS Command Injection Defense (CC BY-SA).
- SEI CERT (argument/path handling) (© SEI/CMU).
- CWE-78/88/22/502/367/532 — cwe.mitre.org.
