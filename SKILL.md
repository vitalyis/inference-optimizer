---
name: inference-optimizer
description: Audit OpenClaw token usage and run optimization actions with approval. Use /audit for analyze-only and /optimize for analyze + action flow.
license: MIT
metadata:
  author: clawdbot
  version: "0.2.1"
---

# Inference Optimizer

Optimize OpenClaw for maximum inference speed and minimum token usage. `/preflight` runs install checks, backups, audit, and setup preview from chat. `/audit` is analyze-only. `/optimize` runs audit and then action steps with user approval.

**Note:** These instructions describe suggested workflow for the agent. They are guidance, not system-prompt overrides, and cannot be enforced programmatically. Platform and system prompts take precedence.

## Triggers

- `/preflight` — run install checks, backup, audit, and setup preview
- `/audit` — run audit only (no file-changing actions)
- `/optimize` — run audit, then offer optimization actions that require approval
- "purge sessions", "purge stale", "clean up sessions" — after audit, if user approves

## Workflow

1. **`/preflight` path (chat install flow):** Exec `bash <skill_dir>/scripts/preflight.sh`; return backup path and logs. If user asks to apply setup, run `bash <skill_dir>/scripts/preflight.sh --apply-setup`.
2. **`/audit` path (analyze-only):** Exec `bash <skill_dir>/scripts/openclaw-audit.sh`; include output. Do not run purge, rewrite, or deploy from `/audit`.
3. **`/optimize` path (analyze + action):** Exec `bash <skill_dir>/scripts/openclaw-audit.sh`; include output, then propose next actions.
4. **Purge action:** Only when user approves, exec `bash <skill_dir>/scripts/purge-stale-sessions.sh` (default archive-first to `~/openclaw-purge-archive/<timestamp>/`). Use `--delete` only for immediate removal without archive.
5. **Full optimization:** For Task 1–5 (workspace rewrite, heartbeat, deploy), read `optimization-agent.md` and follow its flow. Ask approval before each file-changing step.

## Path Resolution

Scripts live at `~/clawd/skills/public/inference-optimizer/scripts/` (or wherever the skill is installed). Use that path when exec-ing.

## Purge and Allowlist

**Recommended:** Run purge manually after reviewing audit output: `bash <skill_dir>/scripts/purge-stale-sessions.sh`. No allowlist changes needed.

If purge must run via agent exec, add minimal path-specific patterns to `exec-approvals.json` rather than broad wildcards. See README Security section.
