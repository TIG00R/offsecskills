# Supply chain, IaC, and CI/CD

Covers OWASP A02/A03/A08:2025. The build and deployment surface, not just app code.

## Software supply chain (A03:2025)

2025 expands this to the whole dependency/build/distribution ecosystem.

- **Dependencies** — lockfile present and committed; versions pinned; no ranges
  that float into the unknown. Flag a reachable vulnerable dependency (use
  `osv-scanner`/`trivy` as a lead, then confirm the vulnerable path is reachable —
  an unused transitive dep is a lead, not a finding). CWE-1104/1395.
- **Typosquatting / dependency confusion** — internal package names also claimable
  on a public registry; suspicious or newly-added unfamiliar packages; install
  scripts (`postinstall`) in dependencies.
- **Integrity (A08)** — unsigned/unverified artifacts, updates, or plugins; no
  hash/signature check on download; deserializing untrusted data (CWE-502).

## CI/CD

- **Unpinned actions/images** — GitHub Actions pinned by tag or branch rather than
  commit SHA (`uses: actions/checkout@v4` → prefer a SHA); base images by mutable
  tag. CWE-494/829.
- **Secrets in CI** — secrets echoed, written to logs, or passed to untrusted PRs;
  `pull_request_target` with checkout of untrusted code; overly broad `GITHUB_TOKEN`
  permissions.
- **Build scripts** — curl-pipe-to-shell from the internet; privileged runners.

## Infrastructure as code

- **Docker** — running as root, no `USER`; secrets baked into layers; `latest`
  base; unnecessary capabilities; exposed docker socket.
- **Kubernetes** — `privileged`/`hostPath`/`hostNetwork`; no resource limits; wide
  RBAC; secrets as plain env.
- **Terraform / cloud** — public storage buckets, `0.0.0.0/0` ingress, plaintext
  secrets in state/vars, over-broad IAM (`*:*`). A02 Security Misconfiguration.

## Configuration (A02:2025)

Debug/verbose errors in production, default credentials, permissive CORS, missing
security headers (for web), directory listing, management endpoints exposed.

## How to use

Installed scanners (osv-scanner/trivy, gitleaks) give leads here more than most
areas — but still confirm reachability and exposure before rating. A misconfig on an internal-only resource
is lower severity than one internet-facing.

## Sources

- OWASP Top 10:2025 A02/A03/A08 — https://top10.owasp.org/2025 (CC BY-SA).
- OWASP Cheat Sheets: Docker, Kubernetes, CI/CD Security, Dependency management
  (CC BY-SA).
- CWE-1104/1395/494/829/502/16 — cwe.mitre.org.
