# Just Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the project rules under `$AGENT_SYSTEM_FOLDER/rules/tools/just/`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Justfile structure, conventions, recipe naming, mandatory recipe set | Authoring or reviewing a `justfile` |
| [`tooling.md`](tooling.md) | Required `just` tooling (purpose, runtime, configuration) | Configuring or invoking `just` |
| [`development-environment.md`](development-environment.md) | Cross-environment satisfaction map for `just` | Provisioning a dev environment that uses `just` |
| [`devcontainer.md`](devcontainer.md) | DevContainer host declarations: feature and lifecycle hooks for `just` (including bash tab completion) | Building or reviewing the `.devcontainer/` for a `just`-using project |
| [`vscode.md`](vscode.md) | VS Code workbench declarations: `just` extensions and editor settings | Configuring the VS Code workbench for `just` |
