# Just VS Code Workbench Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

VS Code is a workbench that runs inside a host environment and provides an editor surface for authoring the project's `justfile` and invoking recipes.

This rule is **prescriptive**. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

No `## Extensions Satisfying Tool Requirements` section is declared for this domain: `just` is a terminal-only CLI with no separate linter or formatter, so editor parity for invoking recipes is provided by VS Code's built-in integrated terminal rather than a marketplace extension. The recommended extension below is an authoring aid for editing the `justfile` itself.

## Authoring Aids

### `skellock.just`

| Field             | Value           |
| ----------------- | --------------- |
| Extension ID      | `skellock.just` |
| Settings          | none            |
| Reads config from | N/A             |

Provides syntax highlighting, snippets, a `just` language definition for `justfile` and `Justfile`, and a "Just: Run Recipe" command for invoking recipes from the editor. The extension declares its own filename associations for `justfile` and `Justfile`, so no workspace `files.associations` setting is required.
