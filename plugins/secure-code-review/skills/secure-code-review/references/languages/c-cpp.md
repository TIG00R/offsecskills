# C / C++ — sources, sinks, footguns

Memory safety dominates. Most of the CWE Top 25 memory entries (787, 416, 125, 120,
121, 122, 476) live here. Follow SEI CERT C/C++.

## Sources

`argv`/`envp`, `read`/`recv`/`fread`, `scanf` family, parsed files/packets,
environment, IPC.

## Dangerous sinks

| Sink | Risk | CWE | Safe equivalent |
| :- | :- | :- | :- |
| `strcpy`, `strcat`, `sprintf`, `gets` | buffer overflow | 120/787 | `strlcpy`/`snprintf` with size; `std::string` |
| `memcpy`/`memmove` with unchecked length | OOB write/read | 787/125 | validate length vs dest size |
| array index from input | OOB | 787/125 | bounds-check before use |
| `free` twice / use after `free` | UAF / double free | 416/415 | null after free; RAII/smart pointers |
| unchecked `malloc`/pointer | NULL deref | 476 | check returns |
| `printf(user)` | format string | 134 | `printf("%s", user)` |
| `system`/`popen`/`exec*` with input | OS cmd inj | 78 | `execve` with arg array, no shell |
| integer math on sizes | int overflow → OOB | 190 | checked arithmetic |
| `atoi`/unchecked parse | input validation | 20 | `strtol` with error checks |

## C++ specifics

- Prefer `std::string`/`std::vector`/`std::span` and RAII over raw buffers/pointers.
- Iterator invalidation, `.at()` vs `operator[]`, dangling references to temporaries.
- Smart-pointer misuse (`.get()` kept past lifetime; cyclic `shared_ptr`).

## Footguns

- Signed/unsigned confusion in length checks.
- `TOCTOU` on files (`access` then `open`). CWE-367.
- Disabled compiler hardening; fixed-size stack buffers with input.

## Safe-by-default (do NOT flag)

Bounds-checked containers, `snprintf`/`strlcpy` with correct sizes, smart pointers
used correctly, `execve` with argument arrays.

## Sources

- SEI CERT C and C++ Coding Standards (© SEI/CMU).
- OWASP Cheat Sheet: C-Based Toolchain Hardening (CC BY-SA).
- CWE-787/416/125/120/121/122/476/134/190/78/367 — cwe.mitre.org.
