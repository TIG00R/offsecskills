# offsecskills

Security skills for [Claude Code](https://code.claude.com/docs/en/skills). A
marketplace of focused, source-cited skills for security review. The first is
**`secure-code-review`** — a deep, security-first source-code review that works
across languages and application types.

Defensive, authorized use only. Read-only by default, every claim cited to
`file:line`, and no invented CWE/OWASP/ASVS IDs.

## Install

```bash
claude plugin marketplace add tig00r/offsecskills
claude plugin install secure-code-review@offsecskills
```

Then, in a session, review the current diff:

```text
/secure-code-review
```

(Plugin skills are namespaced, so `/secure-code-review:secure-code-review` always
works; the bare `/secure-code-review` works unless another command takes the
name.)

## secure-code-review

Security is the primary lens; code quality is a secondary pass. It is built for
every language and app type — web, API, mobile (iOS/Android), desktop, CLI,
embedded/IoT, smart contracts, LLM/agentic, and infrastructure-as-code.

It is **not** a feature-writer or a formatter. It reviews.

### What makes it different

- **It asks first.** A short interview establishes the project's purpose, app
  type, sensitive data, and whether an authorized live instance exists.
- **It models the system.** It derives actors, resources, and a **role matrix**
  (who may do what, object ownership, tenant isolation, field-level permissions)
  and **stops for your confirmation** before hunting access-control flaws. The
  confirmed matrix is ground truth.
- **It finds logic bugs, not just injection.** Workflow/state modeling, races,
  and the OWASP Business Logic Abuse Top 10 drive a pass that static tools miss.
- **It can correlate a live app — only if authorized.** With your written
  authorization and a confirmed scope, it maps live behavior back to the code so
  it understands what the server actually enforces, and (within scope) confirms
  access-control gaps. Read-first, allowlisted, rate-limited, test accounts only.
- **It filters false positives.** Findings need an attacker-controlled path to
  impact and medium+ confidence; High/Critical findings get a blind re-check.
- **It cites everything** to `file:line`, maps each finding to CWE and the
  relevant OWASP/ASVS/API/MASVS IDs, and emits a minimal fix plus a regression
  test.

### Modes

| Invocation | Scope |
| :- | :- |
| `/secure-code-review` | The current uncommitted diff |
| `/secure-code-review src/payments` | A path |
| `/secure-code-review main..HEAD` | A git range |
| `/secure-code-review 123` | A pull request number |
| `/secure-code-review full` | The whole repository (risk-scoped) |

### The flow

1. **Interview** — purpose, app type(s), data sensitivity/compliance, ASVS level
   (default L2), and the live-app question.
2. **Model + confirm the role matrix** — presented to you; the review stops here
   until you confirm or correct it.
3. **Authorized live correlation** — only with written authorization and scope;
   otherwise noted as a coverage gap and reasoned statically.
4. **Sweeps** — threat model (STRIDE per entry point), source→sink, ASVS/API/
   MASVS controls, broken access control vs the matrix, business-logic & races.
5. **Variant hunt** — find every other instance of each confirmed bug.
6. **False-positive pass** — High/Critical re-verified blind by a subagent.
7. **Report** — rated by likelihood × impact, self-checked against the finding
   contract (file:line, evidence, CWE, severity, confidence, fix), then a
   secondary quality pass.

### Optional tools

The skill is useful with nothing but `git`. If these are installed, it uses them
as leads (and skips cleanly if they are absent): `semgrep`, `gitleaks`,
`osv-scanner`/`trivy`, `shellcheck`, and language linters. It never requires
them and never fails because one is missing.

### Safety

Read-only on your code. No writes, commits, pushes, or network calls unless you
ask. Live testing happens only inside your written authorization and agreed
scope — never against out-of-scope hosts, and never with requests that harm data
or availability. Secrets are redacted (first four characters plus location).

## Repository layout

```
offsecskills/
├── .claude-plugin/marketplace.json
├── README.md · LICENSE
└── plugins/secure-code-review/
    ├── .claude-plugin/plugin.json
    └── skills/secure-code-review/
        ├── SKILL.md
        └── references/   (methodology, taxonomies, languages/, app-types/)
```

## Sources and attribution

The skill paraphrases and cites, never copies. Each reference file names its
sources and licenses. Principal sources:

- **OWASP** — Top 10:2025, API Security Top 10 (2023), ASVS 5.0.0, MASVS 2.1.0 /
  MASTG, Top 10 for Business Logic Abuse (2025), Top 10 for LLM Applications,
  Top 10 for Agentic Applications (2026), the Code Review Guide, and the Cheat
  Sheet Series. OWASP content is licensed **CC BY-SA**.
- **MITRE / CISA** — CWE Top 25 (2025) and KEV.
- **SEI CERT** — secure coding standards (C, C++, Java).
- **Google Engineering Practices** — the code-review quality pass.

Trademarks and taxonomies belong to their respective owners. This project is not
affiliated with or endorsed by OWASP, MITRE, CISA, SEI, or Google.

## License

Code: [MIT](LICENSE). Third-party security content: the licenses named in each
reference file (OWASP: CC BY-SA).
