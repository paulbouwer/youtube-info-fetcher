# Markdown Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the project rules under `$AGENT_SYSTEM_FOLDER/rules/formats/markdown/`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Document structure, headings, formatting conventions, naming, authoring style | Authoring or reviewing markdown content |
| [`tooling.md`](tooling.md) | Required markdown tooling: `markdownlint-cli2`, `prettier`, Mermaid | Configuring or invoking markdown linters/formatters |
| [`development-environment.md`](development-environment.md) | Cross-environment satisfaction map — which environments satisfy the markdown tool requirements | Provisioning a dev environment that authors markdown |
| [`devcontainer.md`](devcontainer.md) | DevContainer host declarations: runtimes and tool installation mechanics for markdown tooling | Building or reviewing the `.devcontainer/` for a markdown-authoring project |
| [`vscode.md`](vscode.md) | VS Code workbench declarations: markdown extensions and editor settings | Configuring the VS Code workbench for markdown authoring |
