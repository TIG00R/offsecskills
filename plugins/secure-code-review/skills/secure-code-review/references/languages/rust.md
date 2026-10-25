# Rust — sources, sinks, footguns

Safe Rust prevents most memory bugs. The review focus is `unsafe`, FFI, logic, and
injection — not the borrow checker's job.

## Where the risk is

- **`unsafe` blocks** — raw pointer deref, `get_unchecked`, `transmute`,
  `from_raw_parts`, `set_len`, uninitialized memory. Re-introduces CWE-787/125/416.
  Every `unsafe` needs a justified invariant; flag ones that trust input for
  bounds/lengths.
- **FFI** — `extern "C"`, `bindgen` wrappers: lengths/lifetimes/nullability crossing
  the boundary; C side can violate Rust guarantees.
- **Panics as DoS** — `unwrap`/`expect`/indexing/`unreachable!` on input;
  arithmetic overflow in release (`wrapping`/`checked` as needed). CWE-248/400.

## Injection / app sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `format!` into SQL + raw query | SQL inj | 89 | sqlx/diesel bound params |
| `Command::new("sh").arg("-c")` with input | OS cmd inj | 78 | `Command::new(bin).args([...])` |
| building HTML by hand | XSS | 79 | askama/tera auto-escaping |
| `reqwest::get(user_url)` | SSRF | 918 | allowlist host/scheme |
| path from input to `File::open` | path traversal | 22 | canonicalize + base check |
| `serde` into untrusted with unexpected types | logic | 20 | strict structs; validate |

## Footguns

- `unsafe` wrappers marked safe in the public API without upholding invariants.
- Secrets in `Debug`/`Display`/logs; use zeroizing types.
- Dependency risk: audit `cargo` tree (`cargo audit` as a lead); build scripts
  (`build.rs`) running arbitrary code.

## Safe-by-default (do NOT flag)

Safe Rust slicing, sqlx/diesel bound queries, `Command` with arg arrays, askama/tera
escaping. Do not report "unsafe exists" — report an `unsafe` that trusts untrusted
input or breaks its invariant.

## Sources

- The Rustonomicon (unsafe) — https://doc.rust-lang.org/nomicon/.
- RustSec advisory DB — https://rustsec.org/.
- CWE-787/125/416/89/78/79/918/22/400 — cwe.mitre.org.
