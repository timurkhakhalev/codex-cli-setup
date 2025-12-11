## Codex CLI Setup (Skills Repo)

This repository contains ready‑to‑use skills for the Codex CLI.  
Clone this repository, copy the skills into `~/.codex/skills`, and Codex will automatically discover and use them in any project.

Currently, the main focus is the `subagents` skill, which teaches Codex how to spawn another Codex process (`codex exec`) as a nested “subagent”.

### Demo

[Watch the demo on YouTube](https://www.youtube.com/watch?v=zOaOwiatuxE)

### What’s in this repo

- `README.md` – Main documentation for this setup (copy this file there).
- `subagents/SKILL.md` – Skill that:
  - Runs Codex as a non‑interactive subagent using `codex exec`.
  - Reuses the current project and `AGENTS.md` instructions.
  - Suppresses streaming logs by default (stderr → `/dev/null`) and only shows the final message.
  - Turns logs back on when you need to debug a run.
  - Uses a long `Run` tool timeout (20 minutes) for subagent shell commands.

More skills can be added later in their own folders (for example, `pdf-processing`, `project-review`, etc.).

### Prerequisites

- Codex CLI installed and available in your shell:  
  `codex --version`
- A Git repository where you want to use Codex.

### Installation

> **IMPORTANT!**

The Skills feature is not in production yet (as of Dec 11, 2025); it is currently in alpha. To use it, follow these steps:

1. Install the latest alpha version of the Codex CLI (e.g., `0.70.0-alpha.2`).
2. Enable the skills feature in your `~/.codex/config.toml`:

```config.toml
[features]
web_search_request = true
skills = true
```

3. Download the repository and place `subagents/SKILL.md` into `~/.codex/skills/`:

```bash
git clone git@github.com:timurkhakhalev/codex-cli-setup.git
cd codex-cli-setup
mkdir -p ~/.codex/skills/subagents
cp subagents/SKILL.md ~/.codex/skills/subagents/SKILL.md
```

4. Run `codex`, enter the `/skills` command, and you will see your new skill.

5. Currently (as of Dec 11, 2025), the Codex CLI cannot run itself on macOS due to permission issues. Therefore, you must run the main Codex CLI agent with the `--yolo` flag. On other operating systems, add the following to your `config.toml`:

```config.toml
[sandbox_workspace_write]
writeable_roots = ["~/YOUR_USERNAME/.codex"]
sandbox_mode = "workspace-write"
```

Also, ensure you have write permissions for the `~/.codex` directory:

```sh
sudo chown -R $(whoami) /Users/YOUR_USERNAME/.codex
```

### Using the `subagents` skill

With the skill installed, Codex (the agent) can choose to call `subagents` when it wants to spin up a helper process. That helper:

- Runs via `codex exec` in non‑interactive mode (no command or edit approvals).
- Uses `read-only` sandbox by default unless the flags say otherwise.
- Appends `2>/dev/null` to hide streaming logs and only surface the final message, unless debugging.
- Uses a `Run` tool timeout of at least 20 minutes for the `codex exec` command.

Typical commands this skill is designed around:

```bash
# Automated refactor in the current project
codex exec --full-auto "Update all README links to HTTPS" 2>/dev/null

# Run in a specific project directory
codex exec --cd /path/to/project "Fix failing tests" 2>/dev/null
```

### Extending this setup

If you add more skills:

- Create a new folder per skill (for example, `my-skill/SKILL.md`).
- Keep the skill prompts written as instructions to Codex, not to the end user.
- Optionally document each skill briefly here so people can see what this repo offers.

Once you’re happy with the set of skills, you can publish this repository to GitHub as your recommended Codex CLI setup.

### Author

**Timur Khakhalev**

- Telegram channel: [Тимур Хахалев про AI Coding](https://t.me/the_ai_architect)
- X: [@timurkhakhalev](https://x.com/timurkhakhalev)
