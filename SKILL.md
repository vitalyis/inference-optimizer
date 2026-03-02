---
name: inference-optimizer
description: Audit OpenClaw token usage, purge stale sessions, and optimize inference speed. Use when the user sends /optimize, /audit, asks to purge sessions, or wants a token/workspace audit.
license: MIT
metadata:
  author: clawdbot
  version: "0.2.0"
---

# Inference Optimizer

Optimize OpenClaw for maximum inference speed and minimum token usage. When user sends `/optimize` or `/audit`, exec the audit script and return raw output. When user approves purge, exec the purge script.

## Triggers

- `/optimize` — run audit
- `/audit` — same as /optimize
- "purge sessions", "purge stale", "clean up sessions" — after audit, if user approves

## Workflow

1. **Audit:** When user sends `/optimize` or `/audit`, exec `bash <skill_dir>/scripts/openclaw-audit.sh` and return its output.
2. **Purge:** If user approves purge after audit, exec `bash <skill_dir>/scripts/purge-stale-sessions.sh` (archives to ~/openclaw-purge-archive by default). Return script output. User can run with `--delete` for immediate removal if preferred.
3. **Full optimization:** For Task 1–5 (workspace rewrite, heartbeat, deploy), read `optimization-agent.md` and follow its flow.

## Path Resolution

Scripts live at `~/clawd/skills/public/inference-optimizer/scripts/` (or wherever the skill is installed). Use that path when exec-ing.

## Purge and Allowlist

**Recommended:** Run purge manually after reviewing audit output: `bash <skill_dir>/scripts/purge-stale-sessions.sh`. No allowlist changes needed.

If purge must run via agent exec, add minimal path-specific patterns to `exec-approvals.json` rather than broad wildcards. See README Security section.
