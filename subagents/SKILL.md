---
name: subagents
description: Helps you to run a subagent using codex exec
---

You are Codex running as a parent agent.  
This skill lets you spawn a nested Codex process (a “subagent”) using `codex exec` so it can work with the same project and AGENTS.md instructions.

```shell
codex exec "count the total number of lines of code in this project" 2>/dev/null
```

In non-interactive mode, `codex exec` does not ask for command or edit approvals. By default it runs in `read-only` mode, so it cannot edit files or run commands that require network access.

When you use this skill, follow these logging rules:

- By default, append `2>/dev/null` to your `codex exec` command so only the subagent’s final message is visible to the caller.
- If a run fails, behaves unexpectedly, or the user explicitly asks to see what the inner agent is doing, omit `2>/dev/null` so the full streaming log is visible for debugging.

When you invoke `codex exec` via the `Run` tool (shell command execution), set a generous timeout so long-running subagent work can complete:

- Use a timeout of at least 20 minutes for the `Run` tool unless the user explicitly requests a shorter limit.

Use this skill when you want a focused helper agent (for refactors, audits, scripted operations, or scans) while keeping your main session and context intact.

Use `codex exec --full-auto` to allow file edits. Use `codex exec --sandbox danger-full-access` to allow edits and networked commands, but only when the user clearly permits this level of access.

### Key Flags

- `--full-auto`: Unattended operation with workspace-write sandbox
- `--dangerously-bypass-approvals-and-sandbox` or `--yolo`: Complete hands-off mode (use carefully)
- `--cd <path>`: Set working directory
- `--model <model>` or `-m`: Specify model (e.g., `-m gpt-5.1`)
- `--sandbox`: Sandbox types:
  - `read-only`: No file edits, no networked commands
  - `workspace-write`: Can edit files in the workspace
  - `danger-full-access`: No sandboxing; full access (use with care)
- `--config`: Pass config variables:
  - `model_reasoning_effort`: Model reasoning effort: `low`, `medium`, `high`;

### Models And Reasoning Effort

When choosing a model for the subagent:

- If the user did not specify a model for code edits, default to `gpt-5.1-codex-max` with `model_reasoning_effort=medium`.
- If the user explicitly specified a model, use that exact name.

### Examples

```bash
# As a subagent, perform automated refactoring
codex exec --full-auto "Update all README links to HTTPS" 2>/dev/null

# Run in a specific project directory
codex exec --cd /path/to/project "Fix failing tests" 2>/dev/null

# Use AGENTS.md context for a focused refactor
codex exec --cd /path/to/project "Using this repo's AGENTS.md instructions, refactor the test helpers for clarity and consistency" 2>/dev/null

codex exec --model gpt-5.1 --sandbox workspace-write --config model_reasoning_effort=low < /tmp/some-big-prompt.md 2>/dev/null
```

### Input Methods

```bash
# Pipe prompt from file
codex exec - < prompt.txt 2>/dev/null

# Standard input
echo "Review this code" | codex exec - 2>/dev/null
```

When this skill is invoked, you should decide:

- Whether a subagent is appropriate (e.g., long-running refactor, scan, or analysis).
- Which flags, sandbox level, and model to use based on user intent and risk.
- Whether to keep logs suppressed (`2>/dev/null`) or visible for debugging.
