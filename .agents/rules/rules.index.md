# Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL`, `$DEVCONTAINER_SKILL`, `$DECISION_RECORDS_SKILL`, `$GIT_WORKFLOW_SKILL`, and `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## How to Use This Index

This file is the entry point for all **project rules** in the agent system. It is a **category map** — each row points at a category folder whose own `rules.index.md` lists the leaf rule files. Two-level lazy loading keeps each index small:

1. Find the category below whose `Load when` trigger matches your task
2. Open that category's `rules.index.md`
3. From there, load only the leaf rule files relevant to the task

Project rules under `$AGENT_SYSTEM_FOLDER/rules/` are mutable and may be customised per repository. Skill rules under `$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/` are immutable and ship with the skill. Project rules override skill rules by default when they cover the same area, unless the owning skill marks a specific rule as non-overridable in its own `rules.index.md`.

## Categories

| Category | Covers | Load when |
|----------|--------|-----------|
| [`formats/markdown/`](formats/markdown/rules.index.md) | Markdown authoring rules (core, tooling, dev environment, devcontainer, vscode) | Authoring or reviewing markdown content, or provisioning markdown tooling in a dev environment |
| [`git-workflow/`](git-workflow/rules.index.md) | Project-customisable git policies (branch, commit, pull-request, tooling, dev environment, devcontainer, vscode, plus commit & PR templates) | Running any git-workflow skill action, or provisioning git tooling/CLI in a dev environment |
| [`languages/bash/`](languages/bash/rules.index.md) | Bash/shell rules (core, tooling, security, dev environment, devcontainer, vscode) | Authoring or reviewing shell scripts, or provisioning bash tooling in a dev environment |
| [`languages/go/`](languages/go/rules.index.md) | Go rules (core, tooling, testing, security, dev environment, devcontainer, vscode) | Authoring or reviewing Go code, or provisioning Go tooling in a dev environment |
| [`tools/just/`](tools/just/rules.index.md) | `just` command runner rules (core, tooling, dev environment, devcontainer, vscode) | Authoring or reviewing `justfile` recipes, or provisioning `just` in a dev environment |
| [`work-tracking/`](work-tracking/rules.index.md) | Project-customisable work-tracking policies (issue, work-package, tooling, dev environment, devcontainer, vscode, plus feature/bug/task body templates) | Running any work-tracking skill action, or provisioning the work-tracking provider CLI in a dev environment |
