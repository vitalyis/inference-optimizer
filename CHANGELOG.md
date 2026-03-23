# Changelog

## [0.3.1] - 2026-03-21

### Added

- Release notes under `docs/release-notes/0.3.1.md`.

### Changed

- `SKILL.md` frontmatter: declare install commands (`cliHelp`), credential/state paths (`config.stateDirs` + `config.example`), `links`, and `requires.bins` under `metadata.openclaw` per ClawHub registry schema (per maintainer feedback on OpenClaw scanner metadata).
- `SKILL.md`: social preview image uses absolute `raw.githubusercontent.com` URL (ClawHub / clients that render without a local asset path).
- `social-preview.png`: linear dimensions reduced by one-third (1024×571 → 683×381) for lighter ClawHub listing preview.

## [0.3.0] - 2026-03-14

### Added

- Release notes for v0.3.0 under `docs/release-notes/0.3.0.md`.
- Social preview image usage in `SKILL.md` for consistent package presentation.
- `verify.sh` now checks for legacy `skills/public` and repo-local workspace wiring.

### Changed

- Repositioned the skill from token-first optimization guidance to runtime-first audit guidance.
- Updated `SKILL.md` to check gateway ownership, service health, executable resolution, updater status, allowlist coverage, and plugin provenance before suggesting inference tuning.
- Simplified `README.md` to a leaner landing-page structure modeled on `bug-hunt`, with install, usage, update, uninstall, and safety sections.
- Clarified allowlist guidance around resolved executable paths and bounded NVM wildcards.
- `openclaw-audit.sh` now performs runtime-first checks before token tuning, including workspace command wiring for the installed skill path.
- `setup.sh` now updates a managed workspace block idempotently instead of append-only wiring.
- `optimization-agent.md` now matches the runtime-first audit flow and installed `~/clawd/skills/inference-optimizer` path.

### Fixed

- Added explicit diagnosis rules so warnings alone are not treated as root cause.
- Documented that partial or truncated updater output is inconclusive until version, service state, and logs are checked.
- `/audit` now emits recommended next steps instead of raw token numbers only.
- Installing or reapplying the skill now repairs stale `/preflight`, `/audit`, `/optimize`, and `purge sessions` command wiring on VPS workspaces.

## [0.2.3] - 2026-03-03

- Exec allowlist docs: resolved binary paths (`/usr/bin/bash` on Ubuntu), preflight patterns, and troubleshooting guidance in README and SKILL.
- SKILL.md: ClawHub frontmatter (emoji, os, bins), commands table, Installation section, Security & Allowlist split; README install command `clawhub install inference-optimizer`.

## [0.2.2] - 2026-03-03

- Added `/preflight` chat flow for backup, audit, and setup preview without shell access.
- Split command intent: `/audit` is analyze-only; `/optimize` is analyze plus approved actions.
- Added `scripts/preflight.sh` with timestamped backups and run logs.
- Updated setup and verify scripts to include `/preflight` wiring and checks.
- Updated docs to reflect archive-first purge behavior and command boundaries.

## [0.2.1] - 2026-03-02

Follow-up security review (see [SECURITY.md](SECURITY.md) addendum):

- Replaced "return raw output" / "return output" with passive phrasing
- Added guidance disclaimer in SKILL.md (not system overrides; platform precedence)
- Added Before installing checklist (7 steps) to README
- Manual install: preview before `--apply`
- Added Script reference section (line numbers for review)

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
