# App type: smart contract / web3

On-chain programs (EVM/Solidity, and others). Bugs are public, immutable, and
directly financial. Pair with `languages/solidity.md`.

## Trust model

Everything on-chain is public and adversarial. Any external account or contract can
call any external function in any order, repeatedly, within a transaction they craft
(including flash-loaned capital). Assume maximal attacker capability.

## Top checks

- **Reentrancy** (SWC-107) — state updated after external calls; apply
  checks-effects-interactions and reentrancy guards.
- **Access control** (SWC-105/106) — every privileged/fund-moving function has an
  explicit owner/role check; no missing modifier; `tx.origin` not used for auth.
- **Arithmetic** — ^0.8 checked math; every `unchecked` block justified.
- **External call handling** (SWC-104) — return values checked; no `delegatecall` to
  untrusted addresses (SWC-112).
- **Oracle / price manipulation** — spot-price reads manipulable via flash loans;
  prefer TWAP / manipulation-resistant feeds.
- **MEV / ordering** (SWC-114) — front-running of sensitive ops; slippage/deadline
  bounds; commit-reveal where needed.
- **Randomness** (SWC-120) — no `block.timestamp`/`blockhash` for value; use VRF.
- **Upgradeability** — initializer protection, storage-layout compatibility, admin
  key custody; proxies not left uninitialized.
- **Economic invariants** — can an attacker borrow, reorder, or manipulate price to
  drain value while every `require` passes?

## Review approach

Enumerate external/public functions and, for each, the state it changes, the funds
it moves, who may call it, and what external calls it makes and in what order. Model
the attacker as another contract.

## Sources

- SWC Registry — https://swcregistry.io/.
- OWASP Smart Contract Top 10 — https://owasp.org/www-project-smart-contract-top-10/
  (CC BY-SA).
- Trail of Bits / Consensys best practices (read, not copied).
