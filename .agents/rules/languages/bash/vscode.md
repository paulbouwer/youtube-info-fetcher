# Bash VS Code Workbench Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

VS Code is a workbench that runs inside a host environment and provides an editor surface for productive bash script authoring.

This rule is **prescriptive**. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

Two prescriptive levels apply:

- Extensions in [`## Extensions Satisfying Tool Requirements`](#extensions-satisfying-tool-requirements) MUST be installed. Each pairs with a [`tooling.md`](tooling.md) entry and provides editor parity for the underlying CLI tool.
- Extensions in [`## Authoring Aids`](#authoring-aids) SHOULD be installed. These are recommended productivity extensions with no [`tooling.md`](tooling.md) counterpart.

## Extensions Satisfying Tool Requirements

### `shellcheck`

| Field             | Value                                    |
| ----------------- | ---------------------------------------- |
| Extension ID      | `timonwong.shellcheck`                   |
| Settings          | none                                     |
| Reads config from | [`.shellcheckrc`](tooling.md#shellcheck) |

Provides real-time linting in the editor with diagnostics that mirror the CLI's output exactly, because both surfaces resolve the same configuration file at the repository root.

### `shfmt`

| Field             | Value                                                |
| ----------------- | ---------------------------------------------------- |
| Extension ID      | `foxundermoon.shell-format`                          |
| Settings          | see snippet below                                    |
| Reads config from | [`.editorconfig`](tooling.md#shfmt) (`[*.sh]` block) |

```jsonc
{
  "[shellscript]": {
    "editor.defaultFormatter": "foxundermoon.shell-format",
    "editor.formatOnSave": true,
    "editor.tabSize": 2,
  },
}
```

`editor.formatOnSave` for shell scripts is intentional — it keeps authored content consistently formatted without requiring an explicit CLI invocation. `editor.tabSize: 2` configures the VS Code editor's typing behaviour (auto-indent and the Tab key) to match the indentation that `shfmt` will produce on save; it is an editor setting rather than `shfmt` configuration, so it does not duplicate the `[*.sh]` block in `.editorconfig`.

## Authoring Aids

### `bash-ide-vscode`

| Field             | Value                           |
| ----------------- | ------------------------------- |
| Extension ID      | `mads-hartmann.bash-ide-vscode` |
| Settings          | none                            |
| Reads config from | N/A                             |

Provides language-server-backed editor features for bash — go-to-definition, hover, document symbols, and diagnostics from `bash-language-server`. The language server is bundled inside the extension; no host-level installation is required.

### `bash-debug`

| Field             | Value                 |
| ----------------- | --------------------- |
| Extension ID      | `rogalmic.bash-debug` |
| Settings          | none                  |
| Reads config from | N/A                   |

Provides interactive debugger UI for bash scripts (breakpoints, step execution, variable inspection) for authors who prefer a visual debugging workflow over `set -x` tracing.
