# App type: infrastructure-as-code / CI-CD

Terraform, CloudFormation, Pulumi, Kubernetes manifests/Helm, Docker, Ansible, and
CI pipelines. Detail in `supply-chain-iac-ci.md`; this is the review lens by surface.

## Trust model

A misconfiguration here exposes everything the app protects. CI often holds the most
powerful credentials in the org. Treat pipeline inputs (PR content, branch/tag names,
issue text) as untrusted.

## Cloud / IaC checks (A02 Security Misconfiguration)

- **Network** — `0.0.0.0/0` ingress to sensitive ports; public subnets for private
  resources.
- **Storage** — public buckets/blobs; unencrypted at rest; no access logging.
- **IAM** — wildcard `*:*` / `Action: *`, over-broad roles, long-lived keys instead
  of short-lived/role assumption; privilege escalation paths.
- **Secrets** — plaintext in variables, state files, or committed `.tfvars`; use a
  secret manager.
- **Encryption / logging** — TLS enforced; audit logging on; KMS keys scoped.

## Kubernetes

Privileged/`hostPath`/`hostNetwork`/`hostPID` pods; missing resource limits; wide
RBAC (`cluster-admin`); secrets as plain env; no network policies; images by mutable
tag; running as root.

## Docker

Root user, no `USER`; secrets in layers/history; `latest`/unpinned base; unnecessary
capabilities; exposed docker socket; build args carrying secrets.

## CI/CD (A03 / A08)

- Actions/steps pinned by commit SHA, not tag/branch. CWE-829/494.
- Untrusted input interpolated into shell (`${{ github.event.* }}` → `run:`);
  `pull_request_target` with untrusted checkout; over-broad token permissions.
- Secrets printed to logs; artifacts/caches poisoning; self-hosted runner exposure.

## Sources

- OWASP Top 10:2025 A02/A03 (CC BY-SA); OWASP IaC/CI-CD Cheat Sheets (CC BY-SA).
- CIS Benchmarks (Docker/Kubernetes/cloud) — referenced, not copied.
- CWE-16/732/829/494/798 — cwe.mitre.org.
