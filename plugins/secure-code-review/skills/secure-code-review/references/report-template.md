# Report template

Emit the report in this shape. Keep the headings and labels as written, and
self-check every finding against the contract before presenting: each must carry
`file:line`, evidence, a CWE (or SWC) id, severity, confidence, and a fix.

---

# Security code review — <target>

## Executive summary

2–5 sentences: what was reviewed, the risk posture, and the headline findings
(counts by severity). No jargon.

## Scope

- **Mode / input:** <diff | path | PR #n | range | full>
- **Files reviewed:** <count; list or glob>
- **App type(s):** <...>
- **Languages / frameworks:** <...>
- **ASVS level:** <L1|L2|L3>   **MASVS profile:** <if mobile>
- **Live-app coverage:** <none | authorized, scope …> — if none, say so plainly.

## Confirmed purpose and role matrix

Restate the user-confirmed purpose and the role matrix used for access-control
findings (the table). If not confirmed, say access-control findings were withheld.

## Threat model snapshot

Entry points and the STRIDE concerns that mattered, in a few lines.

## Findings

| ID | Title | Severity | Confidence | CWE | OWASP | ASVS | Location |
| :- | :- | :- | :- | :- | :- | :- | :- |
| F-01 | … | High | High | CWE-89 | A05:2025 / API… | V1 | path:line |

Then one block per finding:

### F-01 — <title>

- **Severity:** <Critical/High/Medium/Low/Info>   **Confidence:** <High/Med/Low>
- **CWE:** CWE-nn   **OWASP:** A0x:2025 (/ APIx:2023 / MASVS-… )   **ASVS:** Vx
- **Location:** `path/to/file:line` (primary)

**Description.** What the flaw is, in plain terms.

**Source → sink trace.** The hops, each with `file:line`:
`path:line` (source) → `path:line` → `path:line` (sink).

**Exploit scenario.** Plain-language, step-by-step impact. **No weaponized
payloads.**

**Evidence.**
```text
<short snippet or redacted live request/response; secrets as "sk_li…" + location>
```

**Fix.** Minimal diff in the project's style:
```diff
- vulnerable line
+ fixed line
```

**Regression test.** A small test that fails on the vulnerable code and passes on
the fix.

**References.** OWASP cheat sheet(s), CWE page, ASVS requirement.

---

## Variants

For each confirmed finding, every other instance of the same root cause found in
scope, with `file:line`.

## Leads not confirmed

Candidates that did not meet the bar, one line each on what's missing. (Keeps the
findings table honest.)

## Positive observations

Security controls done right (so the reader doesn't "fix" them). Cite `file:line`.

## Coverage gaps

What was not reviewed and why (no live app, scanner absent, out-of-scope paths,
time). Be explicit so the reader knows the review's edges.

## Quality observations (secondary)

A short list from `quality-review.md`, each with `file:line`.
