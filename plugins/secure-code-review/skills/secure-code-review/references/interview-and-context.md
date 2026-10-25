# Interview and context

The startup Q&A. Ask once, as a short batch, and **wait** for answers before
scanning. Propose drafts from the repo so the user confirms rather than writes.

## The questions

1. **Purpose** — "In one or two sentences, what is this project's main purpose?"
   If the repo makes it obvious, propose a draft and ask for confirm/correct.
2. **Live app** — "Is there a live/demo instance you want me to interact with
   during the review? If yes: the base URL(s)/host, and written confirmation that
   you own or are authorized to test it, whether it's staging or production, and
   any out-of-scope paths." If no, continue static-only. (This question gates
   `live-app-dast.md`; a URL alone is not authorization.)
3. **App type(s)** — web / API / mobile / desktop / CLI / embedded / smart
   contract / LLM-agentic / infra. Propose a guess from the repo (file extensions
   and manifests).
4. **Sensitive data & compliance** — what assets it handles (PII, payments,
   credentials, health, keys) and any regime (PCI/HIPAA/GDPR/SOC2/…). This sets
   impact.
5. **Assurance level** — ASVS L1/L2/L3 (default **L2**); MASVS profile for mobile.

## Why each matters

- Purpose and data sensitivity set **impact**, so they set severity.
- App type selects which `app-types/*` and `languages/*` references apply.
- The live-app answer decides whether a dynamic correlation pass is even possible,
  and under what authorization.
- The ASVS level sets how many controls you verify (L1 baseline → L3 high-assurance).

## Drafting from the repo (read-only)

To propose the purpose/app-type drafts, read README, package manifests
(`package.json`, `pyproject.toml`, `pom.xml`, `go.mod`, `Cargo.toml`, `Gemfile`,
`*.csproj`), framework config, Dockerfiles, CI, and the route map (grep the framework's route
registrations). Never run the project; read it.

## If the user doesn't answer

Do the parts that don't depend on the answer (crawl, source→sink, language/CWE
review). State your assumption for the rest (e.g. "assuming L2, no live app") and
proceed; note it as a coverage gap. Do not block the whole review on one answer.
The role-matrix confirmation (next file) is the one hard stop.

## Sources

- OWASP ASVS 5.0.0 — assurance levels — https://owasp.org/ASVS/ (CC BY-SA).
- OWASP Code Review Guide v2 — context gathering (CC BY-SA).
