# Review methodology

How to review, independent of taxonomy. Method adapted from the OWASP Code Review
Guide v2 (take the method, not its dated taxonomy).

## Contents

- Risk-based scoping
- Context gathering
- STRIDE threat modeling
- Attack surface & entry-point enumeration
- Source-to-sink analysis
- Transaction analysis
- Code crawling
- Variant enumeration
- Likelihood × impact rating

## Risk-based scoping

You cannot read everything with equal care. Rank by risk and spend effort there:
authentication/session/authorization code; anything parsing untrusted input;
payment, PII, and secrets handling; file and process execution; serialization;
templating/output; native/unsafe code; and recently changed code (`git log`,
blame). For `full` scope, start from entry points and sensitive sinks and expand
outward, not alphabetically.

## Context gathering

Before judging code, know what it is for. Purpose, actors, data sensitivity,
deployment (internet-facing vs internal), framework and its built-in protections,
and the trust boundaries. The interview (see `interview-and-context.md`) and the
role matrix (`business-model-and-roles.md`) produce this context. A finding's
severity depends on it.

## STRIDE threat modeling

Per entry point, ask which of these the code must resist, and whether it does:

- **S**poofing — authentication of the caller/peer.
- **T**ampering — integrity of data in transit and at rest.
- **R**epudiation — logging/audit of security-relevant actions.
- **I**nformation disclosure — confidentiality, error leakage, side channels.
- **D**enial of service — resource limits, unbounded work.
- **E**levation of privilege — authorization, the role matrix.

## Attack surface & entry points

Enumerate everything that accepts external input: HTTP routes/handlers, GraphQL
resolvers, gRPC/RPC methods, message-queue consumers, CLI args, env, files
read, deserializers, webhooks, mobile IPC (intents, URL schemes, deep links),
and inter-agent/tool interfaces. Enumerate web routes by grepping the framework's
route registrations, and note the guard on each. Each entry point is where a source
begins.

## Source-to-sink analysis

A finding is a reachable path from a **source** (attacker-controlled input) to a
**sink** (a dangerous operation) without adequate sanitization. For each
candidate sink from the crawl, trace backward: can untrusted data reach it? What
transforms/validates it on the way? Record the hops as `file:line → file:line`.
No reachable source means no finding — downgrade to a lead.

## Transaction analysis

Follow a whole operation end to end (e.g. "place order", "reset password"):
every step, state change, authorization check, and external call. Logic and
access-control flaws live between steps, not in a single line — a check done in
step 2 but bypassable by calling step 4 directly, a state that should be
unreachable, a replayable idempotency token.

## Code crawling

Grep for candidate sinks and risky APIs using the per-language patterns in
`languages/*`, and run any installed scanners (semgrep/gitleaks/osv-scanner). These
are **leads**, not findings — every hit must be traced and confirmed. Grep is for
locating, not concluding.

## Variant enumeration

When you confirm a bug, it is rarely the only instance. Grep the entire scope for
the same sink, pattern, or root cause and list every other occurrence. One
confirmed SQLi usually means several; one missing authz check means the pattern
repeats.

## Likelihood × impact rating

Rate each confirmed finding by likelihood (reachability, pre-/post-auth,
exposure, exploit complexity, KEV presence) × impact (confidentiality/integrity/
availability, blast radius, privilege gained). Keep **confidence** separate from
severity. See `report-template.md` for the output.

## Sources

- OWASP Code Review Guide v2 — https://owasp.org/www-project-code-review-guide/
  (CC BY-SA). Method only; taxonomy comes from the current OWASP/CWE references.
- Microsoft STRIDE — threat-modeling mnemonic (widely published).
