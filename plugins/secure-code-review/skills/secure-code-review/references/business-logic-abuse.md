# Business logic abuse

Flaws in how an application's own rules are enforced across a workflow — invisible
to pattern-matching SAST because each line is "correct". Based on the OWASP Top 10
for Business Logic Abuse (2025).

## Model the workflow first

For each multi-step operation (checkout, signup, password reset, transfer,
refund), draw the intended state machine: states, allowed transitions, the guard
and role each transition needs, and the idempotency expectation. Bugs are
transitions the code permits that the model forbids.

## The classes (BLA1–BLA10:2025)

- **BLA1 Action Limit Overrun (ALO)** — unsynchronized/repeated actions pass a
  single-use or quota limit (coupon/refund reuse). Often TOCTOU (CWE-367).
- **BLA2 Concurrent Workflow Order Bypass (CWOB)** — a later step runs before
  earlier required steps finish; ship-before-pay, orphaned transition.
- **BLA3 Object State Manipulation (OSM)** — untrusted input bound to internal
  object state overrides protected properties (overlaps mass assignment).
- **BLA4 Malicious Logic Loop (MLL)** — unbounded/poorly-gated loops or recursion
  exhaust resources.
- **BLA5 Artifact Lifetime Exploitation (ALE)** — tokens/sessions/credentials
  valid longer than intended.
- **BLA6 Missing Transition Validation (MTV)** — a step never checks a
  precondition (signup that never verifies eligibility).
- **BLA7 Resource Quota Violation (RQV)** — overwhelming quota/limits for
  advantage or disruption.
- **BLA8 Internal State Disclosure (ISD)** — unintended exposure of internal
  state/data (a data oracle).
- **BLA9 Broken Access Control (BAC)** — role/endpoint checks not enforced (see
  `access-control-deep.md`).
- **BLA10 Shadow Function Abuse (SFA)** — exposed hidden functionality
  (introspection, skipped validation path).

> BLA1–BLA4 are confirmed against the official OWASP project page; BLA5–BLA10
> names are from a secondary summary — verify wording at the OWASP source before
> quoting an exact title in a report.

## What to look for in code

- Steps callable out of order (each handler independent, no server-side state
  check): can finalize run without the pay step? (BLA2/BLA6)
- Check-then-act without a lock/transaction: balance, inventory, coupon, rate
  limit (BLA1, CWE-367). In Go/Java/JS look for read → decide → write on shared
  state with no mutex/row-lock/`SELECT ... FOR UPDATE`.
- Idempotency tokens reused or not enforced (replay).
- Client-trusted price/quantity/total; negative or overflowing amounts.
- State fields set directly from input (BLA3/OSM).
- Unbounded loops/recursion/retries driven by input (BLA4).
- Error/diff/timing responses leaking internal state (BLA8/ISD).

## Confirm, then hunt variants

A logic bug confirmed in one workflow usually recurs. Check sibling workflows for
the same missing guard or non-atomic pattern.

## Sources

- OWASP Top 10 for Business Logic Abuse (2025) —
  https://owasp.org/www-project-top-10-for-business-logic-abuse/ (CC BY-SA).
- CWE-367 (TOCTOU), CWE-841 (Improper Enforcement of Behavioral Workflow) —
  cwe.mitre.org.
