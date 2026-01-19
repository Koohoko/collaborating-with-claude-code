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

- Claude Code CLI installed (`claude --version`) and authenticated (e.g., `ANTHROPIC_API_KEY` or other auth flow).
- Defaults to **full access** (`--full-access`): use only in trusted repos/directories.

## Quick start

```bash
python <script_loc> --cd "/path/to/repo" --PROMPT "Review the auth flow for bypasses; propose fixes as a unified diff."
```

For **read-only** review (avoid edits/commands), add `--no-full-access`:

```bash
python <script_loc> --no-full-access --cd "/path/to/repo" --PROMPT "Review the auth flow and list issues (no code changes)."
```

## Multi-turn sessions

Always capture the returned `SESSION_ID` and pass it back on follow-ups:

```bash
# Start a new session
python <script_loc> --cd "/repo" --PROMPT "Summarize the module boundaries and risky areas."

# Continue the same session
python <script_loc> --cd "/repo" --SESSION_ID "abc123" --PROMPT "Now propose 5 targeted tests for those risks."
```

## Parameters (bridge script)

- `--PROMPT` (req): Instruction to send to Claude Code.
- `--cd` (req): Working directory (typically repo root).
- `--SESSION_ID` (opt): Resume an existing Claude Code session.
- `--model` (opt, default: `claude-opus-4-5-20251101`)
- `--full-access` / `--no-full-access` (opt, default: `--full-access`)
- `--permission-mode` (opt, default: full=`bypassPermissions`, readonly=`plan`)
- `--tools` (opt, default: full=`default`, readonly=`Read,Glob,Grep,LS`)
- `--allowedTools` (opt, default: full=`*`, readonly=`Read,Glob,Grep,LS`)
- `--return-all-messages` (opt): Return the full streamed JSON event list (debug).
- `--timeout-s` (opt, default: 1800s)

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
