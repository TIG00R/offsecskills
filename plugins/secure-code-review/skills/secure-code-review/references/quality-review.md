# Quality review (secondary pass)

After the security review, a short code-quality pass. Keep it clearly separate from
security findings and keep it brief — this is secondary. Based on Google's
engineering practices "What to look for in a code review".

## What to look for

- **Design** — does the change belong here; does it integrate well; is the
  complexity justified or is it over-engineered for needs not yet present?
- **Correctness** — does it do what it intends; edge cases, off-by-one, error
  paths, concurrency; are nulls/None and failures handled?
- **Complexity** — simpler possible? Functions/▁files doing too much; dead code;
  premature abstraction.
- **Tests** — are there tests; do they test behavior and failure, not just the
  happy path; will they catch a regression of the security fixes you propose?
- **Naming & readability** — names reveal intent; consistent with the codebase.
- **Comments** — explain **why**, not what; no stale or misleading comments.
- **Consistency** — follows the project's existing style and idioms.
- **Documentation** — public APIs and non-obvious behavior documented.

## How to report it

A short "Quality observations" section after the security report: a handful of the
highest-value items, each with `file:line` and a concrete suggestion. Do not pad
it, do not restyle the codebase, and never let a style nit outrank a security
finding.

## Sources

- Google Engineering Practices — "What to look for in a code review" —
  https://google.github.io/eng-practices/review/reviewer/looking-for.html
  (CC BY 4.0).
