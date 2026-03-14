# Security

This document records the security and operational hardening choices behind `inference-optimizer`.

## v0.3.0 addendum

This release keeps the command surface unchanged, but tightens how the skill should diagnose and recommend fixes in production OpenClaw environments.

### What changed

- The skill now audits runtime health before suggesting inference tuning.
- The audit order now checks:
  1. gateway ownership and duplicate supervisors
  2. restart loops and failed services
  3. resolved `openclaw` binary path and install type
  4. workspace command wiring for the installed skill path
  5. updater status and allowlist coverage for the resolved path
  6. plugin provenance and unused local extensions
  7. only then context pressure, stale sessions, cache-trace, pruning, and concurrency
- Updater/process diagnosis now has stricter rules:
  - warnings are not root cause by themselves
  - partial or truncated output is inconclusive
  - installed version, service state, and logs must be checked before naming a cause
- Allowlist guidance now explicitly prefers resolved executable paths and bounded NVM wildcards over basename-only rules.
- `README.md` was simplified, with more operational detail kept here instead of the landing page.
- `openclaw-audit.sh` now checks runtime health, workspace command wiring, allowlist coverage, plugin provenance signals, and then token/session overhead.
- `openclaw-audit.sh` now emits a `Recommended next steps` section so the audit produces actionable follow-up instead of raw metrics only.
- `setup.sh` now updates a managed workspace block idempotently and removes legacy references such as:
  - `~/clawdbot/code/scripts/openclaw-audit.sh`
  - `~/clawdbot/code/scripts/purge-stale-sessions.sh`
  - `/clawd/skills/public/inference-optimizer/...`
- `verify.sh` now fails when stale install paths or legacy workspace wiring are still present.

### Why this matters

The March 14, 2026 VPS remediation exposed failure modes that pure token optimization guidance missed:

- duplicate gateway supervisors caused the largest live instability
- updater commands failed from chat because the allowlist covered the wrong path
- warning text from an untracked plugin was incorrectly treated as the updater failure cause
- the docs promised runtime-first checks before the shipped scripts actually performed them

This release updates the skill so those conditions are checked before tuning recommendations are made, and it adds install-time verification so dead VPS paths are caught immediately.

## Operational safety rules

### 1. Treat warnings as signals, not proof

Warnings can point to real issues, but they are not sufficient evidence for root-cause claims.

Examples:

- a plugin provenance warning does not prove the plugin blocked an update
- a restart loop does not prove inference settings are the cause

If output is partial, the correct conclusion is "inconclusive until verified."

### 2. Resolve the real executable path first

OpenClaw allowlists often match resolved binary paths, not just basenames.

Before editing allowlists:

- run `which openclaw` or `command -v openclaw`
- where needed, confirm symlink resolution with `readlink -f`
- match the actual executable path in the allowlist

For versioned NVM installs, prefer bounded patterns like:

```text
/home/ubuntu/.nvm/versions/node/*/bin/openclaw
/home/ubuntu/.nvm/versions/node/*/bin/openclaw *
/home/ubuntu/.nvm/versions/node/*/bin/openclaw **
```

Avoid relying on basename-only entries such as:

```text
openclaw
```

### 3. Keep audit read-only

`/audit` should inspect and report. It should not purge, rewrite, deploy, or restart services as part of the audit step.

### 4. Archive before destructive cleanup

- `purge-stale-sessions.sh` archives by default to `~/openclaw-purge-archive/<timestamp>/`
- use `--delete` only for intentional immediate removal
- inspect archived contents before permanent cleanup

### 5. Preview setup changes before applying

`setup.sh` should be previewed before `--apply`. It changes workspace instruction files and therefore changes agent behavior.

## Prior security review history

## v0.2.1 addendum

**Report:** Pre-scan still flagged "return raw output" and prescriptive phrasing ("return output"). Skill instructs agent to follow a workflow that could coerce behavior. Enforcement of redaction/metadata rules relies on the agent.

**Changes:**

- Replaced "return raw output" and "return output" with passive phrasing: "the script produces metadata that may be relayed"; "include the script's output in your response."
- Added disclaimer in `SKILL.md` that these are workflow instructions, not system-prompt overrides.
- Added a pre-install checklist to the old README structure.
- Manual install showed preview before `--apply`.
- Added script reference guidance for reviewer inspection.

## v0.2.0 remediation summary

### 1. Instruction scope and system-prompt override

Prescriptive prohibitions were replaced with descriptive workflow wording so the skill reads as guidance rather than a system override.

### 2. Sensitive data handling

- Audit outputs metadata only.
- Rewrites must never surface secrets; use `<redacted>` when examples require placeholders.

### 3. Purge and allowlist safety

- Purge archives by default instead of deleting immediately.
- Broad wildcard allowlist guidance was removed in favor of manual execution or path-specific patterns.

### 4. Setup confirmation

- `setup.sh` became preview-first.
- `--apply` became the explicit write path.

## Summary

Current security posture for the skill:

- runtime-first audit guidance
- stricter diagnosis rules for updater/process failures
- resolved-path allowlist guidance
- archive-first cleanup
- preview-first setup changes
- metadata-only audit output
