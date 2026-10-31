# offsecskills

Security skills for [Claude Code](https://code.claude.com/docs/en/skills). A
marketplace of focused, source-cited skills for security review and authorized
assessment:

- **`secure-code-review`** — deep, security-first source-code review across
  languages and application types.
- **`cloud-pentest`** — authorized cloud penetration testing for AWS, Azure /
  Entra ID, GCP, and Kubernetes/containers.
- **`wifi-pentest`** — authorized Wi-Fi / wireless penetration testing across
  802.11 OPN/OWE/WEP/WPA/WPA2/WPA3 and WPA-Enterprise (802.1X/EAP).

Defensive, authorized use only. Every claim cited to its evidence, no invented
CWE/OWASP/ASVS IDs, and no weaponized tooling. `secure-code-review` is read-only
by default; the pentest skills are authorization-gated and least-impact — no
live action without the owner's written authorization and a confirmed scope.

## Install

### Option 1 — Marketplace (recommended, Claude Code)

```bash
claude plugin marketplace add tig00r/offsecskills
```

```bash
claude plugin install secure-code-review@offsecskills
```

```bash
claude plugin install cloud-pentest@offsecskills
```

```bash
claude plugin install wifi-pentest@offsecskills
```

Then, in a session, review the current diff:

```text
/secure-code-review
```

Or start an authorized assessment (each stops for scope + written authorization
before any live action):

```text
/cloud-pentest aws
```

```text
/wifi-pentest
```

Plugin skills are namespaced, so `/secure-code-review:secure-code-review` always
works; the bare `/secure-code-review` works unless another command takes the name.
`claude plugin update secure-code-review` pulls later changes.

### Option 2 — Copy the skill into Claude Code's skills folder

Each skill is a self-contained folder (`SKILL.md` + `references/`), so you can
drop any of them straight into a skills directory instead of using the
marketplace. The examples below use `secure-code-review`; for another skill swap
the name in the path (`plugins/cloud-pentest/skills/cloud-pentest`,
`plugins/wifi-pentest/skills/wifi-pentest`).

**Personal — available in all your projects on this machine:**

```bash
git clone https://github.com/tig00r/offsecskills
mkdir -p ~/.claude/skills
cp -r offsecskills/plugins/secure-code-review/skills/secure-code-review \
  ~/.claude/skills/secure-code-review
```

**Project — just this repo (commit it to share with your team):**

```bash
mkdir -p .claude/skills
cp -r /path/to/offsecskills/plugins/secure-code-review/skills/secure-code-review \
  .claude/skills/secure-code-review
```

Invoke it as `/<skill-name>`. Claude Code picks up a new personal/project
skill within the session; if it doesn't appear, restart or run `/reload-skills`.
Updating is just re-copying the folder (`git pull` first). Installed this way it is
a plain skill, not a plugin, so it has no `plugin-name:` prefix.

### Option 3 — System-wide / other harnesses

Each skill follows the open [Agent Skills](https://agentskills.io) standard — plain
`SKILL.md` + reference files, no bundled code and no runtime injection — so it is
portable beyond a single Claude Code install:

- **Your whole claude.ai account** (Cowork, cloud, and signed-in terminal
  sessions): upload a skill's folder in your claude.ai **Skills** settings.
  Uploads accept only the spec fields (`name`, `description`, `license`,
  `compatibility`, `metadata`, `allowed-tools`), so remove the Claude-Code-only
  `argument-hint:` line from `SKILL.md` before uploading.
- **Any Agent Skills–compatible harness**: point the tool at a skill's folder,
  or copy it into that tool's skills location. The Markdown body and references
  are harness-agnostic; `argument-hint` is ignored elsewhere.
- **Every user on a machine** (Claude Code, admin): place the folder in
  `.claude/skills/` inside your [managed-settings directory](https://code.claude.com/docs/en/managed-settings).

## secure-code-review

Security is the primary lens; code quality is a secondary pass. It is built for
every language and app type — web, API, mobile (iOS/Android), desktop, CLI,
embedded/IoT, smart contracts, LLM/agentic, and infrastructure-as-code.

It is **not** a feature-writer or a formatter. It reviews.

### What makes it different

- **It asks first.** A short interview establishes the project's purpose, app
  type, sensitive data, and whether an authorized live instance exists.
- **It models the system.** It derives actors, resources, and a **role matrix**
  (who may do what, object ownership, tenant isolation, field-level permissions)
  and **stops for your confirmation** before hunting access-control flaws. The
  confirmed matrix is ground truth.
- **It finds logic bugs, not just injection.** Workflow/state modeling, races,
  and the OWASP Business Logic Abuse Top 10 drive a pass that static tools miss.
- **It can correlate a live app — only if authorized.** With your written
  authorization and a confirmed scope, it maps live behavior back to the code so
  it understands what the server actually enforces, and (within scope) confirms
  access-control gaps. Read-first, allowlisted, rate-limited, test accounts only.
- **It filters false positives.** Findings need an attacker-controlled path to
  impact and medium+ confidence; High/Critical findings get a blind re-check.
- **It cites everything** to `file:line`, maps each finding to CWE and the
  relevant OWASP/ASVS/API/MASVS IDs, and emits a minimal fix plus a regression
  test.

### Modes

| Invocation | Scope |
| :- | :- |
| `/secure-code-review` | The current uncommitted diff |
| `/secure-code-review src/payments` | A path |
| `/secure-code-review main..HEAD` | A git range |
| `/secure-code-review 123` | A pull request number |
| `/secure-code-review full` | The whole repository (risk-scoped) |

### The flow

1. **Interview** — purpose, app type(s), data sensitivity/compliance, ASVS level
   (default L2), and the live-app question.
2. **Model + confirm the role matrix** — presented to you; the review stops here
   until you confirm or correct it.
3. **Authorized live correlation** — only with written authorization and scope;
   otherwise noted as a coverage gap and reasoned statically.
4. **Sweeps** — threat model (STRIDE per entry point), source→sink, ASVS/API/
   MASVS controls, broken access control vs the matrix, business-logic & races.
5. **Variant hunt** — find every other instance of each confirmed bug.
6. **False-positive pass** — High/Critical re-verified blind by a subagent.
7. **Report** — rated by likelihood × impact, self-checked against the finding
   contract (file:line, evidence, CWE, severity, confidence, fix), then a
   secondary quality pass.

### Optional tools

The skill is useful with nothing but `git`. If these are installed, it uses them
as leads (and skips cleanly if they are absent): `semgrep`, `gitleaks`,
`osv-scanner`/`trivy`, `shellcheck`, and language linters. It never requires
them and never fails because one is missing.

### Safety

Read-only on your code. No writes, commits, pushes, or network calls unless you
ask. Live testing happens only inside your written authorization and agreed
scope — never against out-of-scope hosts, and never with requests that harm data
or availability. Secrets are redacted (first four characters plus location).

## cloud-pentest

Authorized penetration testing of a cloud environment — AWS, Azure / Entra ID,
GCP, and the Kubernetes/containers on top. The cloud's perimeter is the IAM
graph, so the work is mostly *enumerate the identities and trust relationships,
then find the path to admin or to the data*.

It is **not** a code reviewer, and **not** for targets you cannot prove you are
authorized to test.

### What makes it different

- **Authorization is a hard stop.** No live request — not even an "enumerate"
  call — runs before you confirm, in writing, the owner, the in-scope
  accounts/subscriptions/projects and exclusions, prod vs non-prod, and which
  action classes are allowed (read-only, state-changing privesc confirmation,
  cracking, or test-account phishing).
- **Least impact.** Read/describe/list by default; anything that changes state,
  phishes, or could disrupt runs only inside the scope and only after you
  confirm that step. No data destruction, no persistence left behind.
- **It maps paths, not just settings.** Privilege-escalation and
  lateral-movement chains across services and accounts (metadata-token theft,
  `PassRole`/`AssumeRole`, service-account impersonation, Key Vault → token).
- **Every finding pairs a fix with its detection** — the least-privilege change
  and the log source (CloudTrail / Entra logs / Cloud Audit Logs) that catches
  it. Evidence is the exact command plus output, secrets redacted.

### Modes

| Invocation | Scope |
| :- | :- |
| `/cloud-pentest aws` | An AWS account |
| `/cloud-pentest azure` | An Azure / Entra ID tenant |
| `/cloud-pentest gcp` | A GCP project/org |
| `/cloud-pentest k8s` | A Kubernetes cluster / containers |

## wifi-pentest

Authorized Wi-Fi / wireless testing across 802.11 OPN/OWE/WEP/WPA/WPA2/WPA3-SAE
and WPA-Enterprise (802.1X/EAP). Wireless testing is active by nature — it
touches RF that reaches devices and people beyond the target — so the gate and
the no-collateral rules come first.

It is **not** for networks you cannot prove authorization for, and it does not
jam, broadcast-deauth, or intercept third-party traffic.

### What makes it different

- **RF authorization is a hard stop.** No transmit, capture, deauth, or rogue AP
  before you confirm, in writing, the owner, the in-scope SSID(s)/BSSID(s) and
  client devices, the location, and the allowed classes. Offline cracking of a
  capture you own is the one thing that needs no RF authorization.
- **Targeted, never broad.** Deauth and downgrade frames go to one in-scope
  BSSID/client — never a whole channel or area. No third-party traffic content
  is read or stored; a weak key is proven by recovering the key, not by reading
  victims' data.
- **Attack by encryption type.** OPN/captive-portal, OWE, WEP, WPA/WPA2-PSK
  (4-way handshake, PMKID, targeted deauth), WPS, WPA3-SAE (MFP check,
  downgrade, online brute force), evil twin, and enterprise EAP (rogue-AP
  credential capture, relay, stolen-CA).
- **Proof, then stop.** Capture one handshake / confirm one PMKID / recover one
  test key; no persistent rogue infrastructure. Each finding pairs a fix
  (WPA3-SAE + PMF, EAP-TLS with server-cert validation, strong PSK, disable WPS)
  with the WIDS signal that detects it, and the host is restored afterward.

### Modes

| Invocation | Scope |
| :- | :- |
| `/wifi-pentest` | On-air assessment of the in-scope network(s) |
| `/wifi-pentest <SSID/BSSID>` | A named target |
| `/wifi-pentest <capture.pcap>` | Offline work on a capture you own |

## Repository layout

```
offsecskills/
├── .claude-plugin/marketplace.json
├── README.md · LICENSE
└── plugins/
    ├── secure-code-review/
    │   ├── .claude-plugin/plugin.json
    │   └── skills/secure-code-review/
    │       ├── SKILL.md
    │       └── references/   (methodology, taxonomies, languages/, app-types/)
    ├── cloud-pentest/
    │   ├── .claude-plugin/plugin.json
    │   └── skills/cloud-pentest/
    │       ├── SKILL.md
    │       └── references/   (authorization, methodology, aws, azure, gcp,
    │                          kubernetes-containers, reporting)
    └── wifi-pentest/
        ├── .claude-plugin/plugin.json
        └── skills/wifi-pentest/
            ├── SKILL.md
            └── references/   (authorization, setup-and-recon, attacks-by-
                               encryption, enterprise-eap, cracking, reporting)
```

## Sources and attribution

Every skill paraphrases and cites, never copies. Each reference file names its
sources and licenses. Principal sources:

- **OWASP** — Top 10:2025, API Security Top 10 (2023), ASVS 5.0.0, MASVS 2.1.0 /
  MASTG, Top 10 for Business Logic Abuse (2025), Top 10 for LLM Applications,
  Top 10 for Agentic Applications (2026), the Code Review Guide, the Web
  Security Testing Guide, and the Cheat Sheet Series. OWASP content is licensed
  **CC BY-SA**.
- **MITRE / CISA** — CWE Top 25 (2025) and KEV; the ATT&CK Cloud and Containers
  matrices (technique names only).
- **NIST** — SP 800-115 (testing methodology) and SP 800-153 (securing wireless
  LANs); U.S. Government, public domain.
- **CIS** — Benchmarks for AWS / Azure / GCP / Kubernetes (control references).
- **SEI CERT** — secure coding standards (C, C++, Java).
- **Google Engineering Practices** — the code-review quality pass.
- **Vendor documentation** — AWS, Microsoft Azure / Entra ID, and Google Cloud
  security and CLI docs; the tool projects named in the pentest references
  (aircrack-ng, hashcat, hcxtools, eaphammer, AADInternals, ROADtools,
  MicroBurst, and others), each under its own license.
- **Cheat-Book** — the owner's own notes at https://cheatbook.tig00r.me, the
  basis for the `cloud-pentest` and `wifi-pentest` techniques.

Trademarks and taxonomies belong to their respective owners. This project is not
affiliated with or endorsed by OWASP, MITRE, CISA, NIST, CIS, SEI, Google,
Amazon, or Microsoft.

## License

Code: [MIT](LICENSE). Third-party security content: the licenses named in each
reference file (OWASP: CC BY-SA).
