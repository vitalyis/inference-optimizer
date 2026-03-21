# Security

Security posture and safe-use expectations for `inference-optimizer`. Per-version change lists live in [CHANGELOG.md](CHANGELOG.md).

## Reporting a vulnerability

Use [GitHub Security Advisories](https://github.com/vitalyis/inference-optimizer/security/advisories/new) for private reports, or open an issue for non-sensitive questions.

## Current posture

- **Runtime before tuning:** Audit gateway ownership, services, resolved `openclaw` path, workspace wiring for this skill, updater/allowlist coverage, plugin signals, then session/context behavior—before inference or token tuning.
- **Audit is read-only:** `/audit` inspects and reports; it does not purge, rewrite workspace files, deploy, or restart services.
- **Diagnosis:** Warnings are not root cause by themselves; partial or truncated output is inconclusive until version, service state, and logs are verified.
- **Allowlists:** Match resolved paths (`which`, `command -v`, `readlink -f`). Prefer bounded NVM patterns over basename-only `openclaw` entries.

```text
/home/ubuntu/.nvm/versions/node/*/bin/openclaw
/home/ubuntu/.nvm/versions/node/*/bin/openclaw *
/home/ubuntu/.nvm/versions/node/*/bin/openclaw **
```

- **Purge:** `purge-stale-sessions.sh` archives by default to `~/openclaw-purge-archive/<timestamp>/`; use `--delete` only for intentional immediate removal.
- **Setup:** `setup.sh` is preview-first; `--apply` changes workspace instruction files and agent-facing behavior.
- **Data:** Audit scripts aim for metadata in outputs; do not paste secrets; use placeholders in examples.

## Release notes (security-relevant)

**v0.3.0** — `openclaw-audit.sh` follows the runtime-first order above and emits **Recommended next steps**. `setup.sh` updates a managed workspace block and removes legacy paths (`~/clawdbot/code/scripts/...`, `/clawd/skills/public/inference-optimizer/...`). `verify.sh` fails if stale install paths or legacy wiring remain.

**v0.2.1** — Wording adjusted to avoid coercive “return output” phrasing; `SKILL.md` states workflow text is not a system-prompt override.

**v0.2.0** — Descriptive workflow wording; purge archives by default; preview-first `setup.sh` with explicit `--apply`; narrower allowlist guidance; audit metadata-only discipline.
