# Go Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the project rules under `$AGENT_SYSTEM_FOLDER/rules/languages/go/`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Project structure, naming, coding style, module conventions | Authoring or reviewing Go code |
| [`tooling.md`](tooling.md) | Required Go tooling: `go`, `golangci-lint`, `gofmt` (purpose, runtime, configuration) | Configuring or invoking Go tooling |
| [`testing.md`](testing.md) | Test naming, table-driven tests, mocking, fixtures, integration tests | Authoring or reviewing Go tests |
| [`security.md`](security.md) | Prohibited practices, secure practices, Go-specific risks | Reviewing Go code for security |
| [`development-environment.md`](development-environment.md) | Cross-environment satisfaction map for Go tooling | Provisioning a dev environment for a Go project |
| [`devcontainer.md`](devcontainer.md) | DevContainer host declarations: runtimes and tool installation mechanics for Go | Building or reviewing the `.devcontainer/` for a Go project |
| [`vscode.md`](vscode.md) | VS Code workbench declarations: Go extensions and editor settings | Configuring the VS Code workbench for Go authoring |
