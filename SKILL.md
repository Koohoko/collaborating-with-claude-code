---
name: collaborating-with-claude-code
description: "Delegate code implementation/review/debugging/alternatives to Claude Code via a JSON bridge script. Multi-turn via SESSION_ID."
metadata:
  short-description: Use Claude Code as a collaborator
---

# Collaborating with Claude Code

Use this skill for second opinions, code review, test design, or code implementations/alternatives. The bridge runs `claude` (Claude Code) non-interactively ("print" mode) and returns JSON.

The script is located at `~/.codex/skills/collaborating-with-claude-code/scripts/claude_code_bridge.py`.

## Timing

Claude Code often needs **1–2+ minutes** per task.
- Prefer running the bridge directly (no `&`); increase `--timeout-s` as needed (default: 1800s).
- Do **NOT** redirect stdout to a file (e.g. `> /tmp/out.json`).

## Context

- Do **NOT** read the script unless you are modifying it; 
- Before running the script, ALWAYS use `python <script_loc> --help` to get the usage instructions.

## Requirements

- Claude Code CLI installed (`claude --version`) and authenticated (e.g. via your normal Claude Code login/token flow).

## Default

- Defaults to **full access** (`--full-access`): use only in trusted repos/directories.
- Defaults to **extended thinking ON** (can disable via `--no-extended-thinking`).
- Defaults to **step mode ON** (can disable via `--step-mode off`).

## Output format

The bridge prints JSON:

```json
{"success": true, "SESSION_ID": "abc123", "agent_messages": "…Claude output…", "all_messages": []}
```

## Recommended delegation patterns

- **Guided coding**: "Implement the code for [feature] following these specific steps/constraints."
- **Second opinion**: "Propose an alternative approach and tradeoffs."
- **Code review**: "Find bugs, race conditions, security issues; propose fixes."
- **Test design**: "Write a test plan + edge cases; include example test code."
- **Diff review**: "Review this patch; point out regressions and missing cases."
