# Bash Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the project rules under `$AGENT_SYSTEM_FOLDER/rules/languages/bash/`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Script structure, naming, coding style, script/variable/function conventions | Authoring or reviewing bash scripts |
| [`tooling.md`](tooling.md) | Required bash tooling: `shellcheck`, `shfmt` (purpose, runtime, configuration) | Configuring or invoking bash linters/formatters |
| [`security.md`](security.md) | Prohibited practices, secure practices, bash-specific risks | Reviewing bash scripts for security |
| [`development-environment.md`](development-environment.md) | Cross-environment satisfaction map for bash tooling | Provisioning a dev environment for a bash-authoring project |
| [`devcontainer.md`](devcontainer.md) | DevContainer host declarations: runtimes and tool installation mechanics for bash | Building or reviewing the `.devcontainer/` for a bash project |
| [`vscode.md`](vscode.md) | VS Code workbench declarations: bash extensions and editor settings | Configuring the VS Code workbench for bash authoring |
