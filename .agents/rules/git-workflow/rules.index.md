# Git Workflow Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, and `$REPO_PROVIDER` from root `AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the project rules under `$AGENT_SYSTEM_FOLDER/rules/git-workflow/`. These rules customise the git-workflow skill's behaviour for this project. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`branch.md`](branch.md) | Branch types, validation regex, naming examples | Running Create Branch |
| [`commit.md`](commit.md) | Commit types, scopes, AI trailer configuration, signing policy | Running Commit |
| [`templates/commit.md`](templates/commit.md) | Commit message templates with examples | Running Commit |
| [`pull-request.md`](pull-request.md) | PR title format, description structure, AI usage tracking, character limits | Running Create Pull Request |
| [`templates/pull-request.md`](templates/pull-request.md) | PR description template | Running Create Pull Request |
| [`tooling.md`](tooling.md) | Tool catalog for `git`, `git-lfs`, `gh` (purpose, runtime, configuration) | Configuring or invoking git tooling |
| [`development-environment.md`](development-environment.md) | Cross-environment satisfaction map for git tooling | Provisioning a dev environment for any project (git is always required) |
| [`devcontainer.md`](devcontainer.md) | DevContainer host declarations: features and lifecycle hooks for `git`, `git-lfs`, `gh` | Building or reviewing the `.devcontainer/` |
| [`vscode.md`](vscode.md) | VS Code workbench declarations: built-in `vscode.git` extension only | Configuring the VS Code workbench |
