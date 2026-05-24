# Work Tracking Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, `$WORK_TRACKING_PROVIDER`, and `$WORK_TRACKING_PROJECT` from root `AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the project rules under `$AGENT_SYSTEM_FOLDER/rules/work-tracking/`. These rules customise the work-tracking skill's behaviour for this project. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`issue.md`](issue.md) | Title format, type set, type→branch-type mapping, board status names, section heading strings, acceptance criteria authoring | Running Create Issue or Update Issue |
| [`work-package.md`](work-package.md) | Frontmatter conventions, draft phase rules, file section conventions, cross-references with decision records and learnings | Running Create Work Package or Update Work Package |
| [`templates/feature.md`](templates/feature.md) | Issue body template for Feature type | Creating a Feature issue |
| [`templates/bug.md`](templates/bug.md) | Issue body template for Bug type | Creating a Bug issue |
| [`templates/task.md`](templates/task.md) | Issue body template for Task type | Creating a Task issue |
| [`tooling.md`](tooling.md) | Tool catalog: `gh` (purpose, runtime, configuration) | Configuring or invoking the work-tracking provider CLI |
| [`development-environment.md`](development-environment.md) | Cross-environment satisfaction map for the provider CLI | Provisioning a dev environment for any project that uses the work-tracking provider |
| [`devcontainer.md`](devcontainer.md) | DevContainer host declarations: feature and post-provisioning authentication contract for `gh` | Building or reviewing the `.devcontainer/` |
| [`vscode.md`](vscode.md) | VS Code workbench declarations: no extensions installed; rationale for enforcing rules via CLI and agent surface | Configuring the VS Code workbench |
