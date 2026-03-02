# Changelog

## [0.2.0] - 2026-03-02

Security remediation (see [SECURITY.md](SECURITY.md)):

- Purge: archive by default; `--delete` for immediate removal
- Setup: preview by default; `--apply` to modify workspace
- Instructions: descriptive wording instead of prescriptive prohibitions
- Data handling: audit metadata only; rewrites use `<redacted>` for secrets
- Allowlist: prefer manual purge; path-specific patterns if exec required

---

## [0.1.0] - 2026-03-02

- Initial release: audit script, purge script, setup, verify, optimization-agent.md.
