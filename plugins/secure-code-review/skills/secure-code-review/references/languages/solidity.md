# Solidity / smart contracts — sources, sinks, footguns

On-chain code: bugs are usually unrecoverable and directly financial. Pair with
`app-types/smart-contract.md`. Use SWC and known DeFi pitfalls as the taxonomy.

## Sources

`msg.sender`, `msg.value`, `tx.origin`, call data / function args, return data from
external calls, oracle inputs, block variables.

## Core vulnerability classes

| Class | Risk | SWC / CWE | Guard |
| :- | :- | :- | :- |
| Reentrancy | state changed after external call | SWC-107 / CWE-841 | checks-effects-interactions; `nonReentrant` |
| Unchecked external call / `.call` return | silent failure | SWC-104 | check return; prefer explicit handling |
| `tx.origin` auth | phishing/auth bypass | SWC-115 | use `msg.sender` |
| Integer over/underflow (pre-0.8 / `unchecked`) | fund math | SWC-101 | ^0.8 checked math; justify `unchecked` |
| Access control on sensitive fns (`onlyOwner` missing) | takeover | SWC-105/106 | explicit modifiers; role checks |
| `delegatecall` to untrusted | storage/ctx hijack | SWC-112 | never delegatecall input addresses |
| Bad randomness (`blockhash`, `timestamp`) | predictable | SWC-120 | VRF / commit-reveal |
| Price/oracle manipulation (spot price, flash loan) | economic | — | TWAP; manipulation-resistant oracles |
| Front-running / MEV | ordering | SWC-114 | commit-reveal; slippage bounds |
| `selfdestruct` / uninitialized proxy | loss/takeover | SWC-106 | guard; initialize proxies |

## Review focus

- External calls: order of state updates vs calls (reentrancy); who can be called.
- Every state-mutating / fund-moving function: access control and input validation.
- Upgradeable proxies: initializer protection, storage layout, admin keys.
- Economic assumptions: can an attacker move the price, borrow, or reorder to profit?
- Visibility (`public`/`external`/`internal`), `payable`, and fallback/`receive`.

## Footguns

- Floating pragma; outdated compiler.
- Assuming `require` messages or events equal access control.
- Trusting off-chain input without on-chain verification.

## Sources

- SWC Registry — https://swcregistry.io/ (classes referenced, not copied).
- OWASP Smart Contract Top 10 — https://owasp.org/www-project-smart-contract-top-10/
  (CC BY-SA).
- Consensys / Trail of Bits best practices (read, not copied).
