---
name: inference-optimizer
description: Audit OpenClaw runtime health first, then optimize inference speed and token usage with approval. Use /audit for analyze-only and /optimize for analyze + action flow.
license: MIT
metadata:
  author: vitalyis
  version: "0.3.0"
  openclaw:
    emoji: "⚡"
    os:
      - linux
    bins:
      - bash
---

![Inference Optimizer](social-preview.png)

# Inference Optimizer

Audit OpenClaw runtime health first. Optimize inference speed and token usage second.

## Commands

| Command | Behavior |
|--------------|----------|
| `/preflight` | Install checks, backup, audit, and setup preview |
| `/audit` | Analyze-only; check runtime health before suggesting tuning |
| `/optimize` | Audit + propose remediation or optimization actions with per-step approval |
| `purge sessions` | After audit, if user approves, archive stale sessions; use `--delete` for immediate removal |

> These instructions guide agent behavior. Platform and system prompts take precedence; they cannot be enforced programmatically.

## Installation

**ClawHub:**
```bash
clawhub install inference-optimizer
```

**Manual:**
```bash
git clone https://github.com/vitalyis/inference-optimizer.git ~/clawd/skills/public/inference-optimizer
bash ~/clawd/skills/public/inference-optimizer/scripts/setup.sh        # preview
bash ~/clawd/skills/public/inference-optimizer/scripts/setup.sh --apply  # apply after review
```

**Verify:** `bash <skill_dir>/scripts/verify.sh`

## Workflow

### Audit and remediation branch

1. **`/preflight`**: Exec `bash ~/clawd/skills/public/inference-optimizer/scripts/preflight.sh`. Append `--apply-setup` only if the user asks to apply setup.
2. **`/audit`**: Exec `bash <skill_dir>/scripts/openclaw-audit.sh`. Use the script output plus direct environment checks to inspect this order:
   - gateway ownership and duplicate supervisors
   - restart loops or failed services
   - resolved `openclaw` binary path and install type
   - updater status and allowlist coverage for the resolved path
   - plugin provenance and unused local extensions
   - only then context pressure, stale sessions, cache-trace, pruning, and concurrency
3. **Diagnosis rule**: Do not conclude from warnings alone. If process output is partial or truncated, report the result as inconclusive and verify installed version, service state, and logs before naming a cause.

### Optimization branch

1. **`/optimize`**: Run the audit flow first, include the script output in the response, then propose next actions with approval before each file-changing step.
2. **Purge**: Only on explicit approval, run `bash <skill_dir>/scripts/purge-stale-sessions.sh`. It archives to `~/openclaw-purge-archive/<timestamp>/` by default. Use `--delete` for immediate removal without archive.
3. **Full optimization (Tasks 1-5)**: Read `optimization-agent.md` and follow its flow. Ask approval before every file-changing step.

## Path Resolution

Scripts live at `~/clawd/skills/public/inference-optimizer/scripts/` or wherever the skill is installed. Always resolve `<skill_dir>` to the actual install path before exec.

## Security and Allowlist

Add these to `exec-approvals.json` so `/preflight` runs without interruption on Ubuntu:

```text
/usr/bin/bash
/usr/bin/bash *
/usr/bin/bash **
```

Before editing any allowlist:

- Resolve the real executable path with `which`, `command -v`, or `readlink -f`.
- Prefer exact paths or bounded wildcards for versioned NVM installs, for example `/home/ubuntu/.nvm/versions/node/*/bin/openclaw *`.
- Do not assume basename-only rules such as `openclaw` are sufficient.

For purge via agent exec, add path-specific patterns only. Avoid broad wildcards. See `README.md` and `SECURITY.md` for operational detail.
