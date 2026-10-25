# False-positive discipline

The difference between a useful review and noise. Report only what you can defend.
Prefer a few verified findings over many speculative ones.

## The bar to report

A finding needs **both**:

1. A concrete, attacker-controlled path to impact (a source reaches the sink, from
   a real entry point), and
2. Confidence ≥ medium.

Anything below the bar goes in a short **"Leads not confirmed"** list with one line
on what's missing, not in the findings table.

## Five questions per candidate

1. **Who controls the input?** Truly attacker-controlled, or internal/constant?
2. **Is it reachable** from an entry point, in a configuration that ships?
3. **What mitigates it** upstream/downstream — framework auto-escaping, ORM
   parameterization, a validated schema, middleware, the confirmed role checks?
4. **What does the attacker gain** — concretely?
5. **Does the exploit path survive** the real control flow, not just the one line?

If a mitigation fully covers it, it is not a finding. Say so in the lead list so
the reader knows you checked.

## Default-exclude unless strong evidence

- Generic DoS / "no rate limit" opinions with no specific amplification.
- Missing HTTP security headers on non-web code.
- Issues only in tests, fixtures, examples, or generated code.
- Theoretical timing attacks without a realistic oracle.
- ReDoS without reachable untrusted input.
- "Outdated library" with no reachable vulnerable code path.
- Anything the framework already mitigates by default (e.g. ORM-parameterized
  queries, auto-escaped templates, built-in CSRF tokens) unless misused.

## Known safe patterns (do NOT report)

- Parameterized queries / prepared statements / ORM query builders with bound
  params.
- Context-aware auto-escaping templates (Jinja autoescape on, React JSX text,
  Razor) used normally — flag only explicit bypasses (`|safe`,
  `dangerouslySetInnerHTML`, `Html.Raw`).
- A route correctly guarded per the confirmed role matrix.
- `jwt.verify` with a pinned algorithm.
- CSPRNG use for tokens.

## High/Critical: blind re-verification

For each High/Critical, spawn a subagent to re-verify the finding **blind** — give
it the code and the claim, not your reasoning — and keep only what it independently
reproduces. Drop or downgrade the rest. This catches the confirmation bias of the
reviewer who found it.

## Sources

- Trail of Bits review practices (fp-check, sharp-edges) —
  https://github.com/trailofbits/skills (read, not copied).
- Anthropic claude-code-security-review — false-positive filtering —
  https://github.com/anthropics/claude-code-security-review (read, not copied).
