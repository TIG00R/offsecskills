# App type: embedded / IoT / firmware

Constrained devices, firmware, RTOS, C/C++ dominant. Memory safety and update
integrity are the headline risks. Pair with `languages/c-cpp.md` and `rust.md`.

## Entry points

Network stacks (often custom), serial/UART, Bluetooth/BLE, radio, USB, sensor/bus
input, OTA update payloads, debug interfaces (JTAG/UART), bootloader.

## Top checks

- **Memory safety** — parsers for network/radio/sensor input: overflow, OOB,
  integer overflow on lengths. CWE-787/125/120/190. The main remote attack surface.
- **Update / boot integrity** — signed, verified firmware; secure/verified boot; no
  downgrade; keys not shared across the fleet. CWE-494/347.
- **Hardcoded / default credentials** — per-device secrets, not a global key baked
  in firmware. CWE-798/1392. (A recurring IoT failure.)
- **Secrets at rest** — keys/certs in flash; use secure element / TrustZone where
  available; flash readout protection.
- **Debug interfaces** — JTAG/serial consoles disabled or authenticated in
  production.
- **Transport** — TLS with real cert validation on constrained clients; no plaintext
  control channels.
- **Weak crypto / RNG** — hardware RNG / proper entropy; no DES/RC4/ECB; no
  home-grown crypto.

## Review notes

- Treat all external bus/radio input as untrusted; bound every copy.
- Watch for `strcpy`/`memcpy`/`sprintf` on packet fields; fixed buffers vs variable
  length fields.
- Fleet-wide shared secrets turn one extraction into a total compromise — flag them.

## Sources

- OWASP IoT / Firmware Security guidance —
  https://owasp.org/www-project-internet-of-things/ (CC BY-SA).
- SEI CERT C/C++ (© SEI/CMU).
- CWE-787/125/120/190/494/347/798/1392 — cwe.mitre.org.
