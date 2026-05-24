# Markdown VS Code Workbench Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

VS Code is a workbench that runs inside a host environment and provides an
editor surface for productive markdown authoring.

This rule is **prescriptive**. Tool requirements are catalogued in
[`tooling.md`](tooling.md); the cross-environment satisfaction map is in
[`development-environment.md`](development-environment.md).

Two prescriptive levels apply:

- Extensions in [`## Extensions Satisfying Tool Requirements`](#extensions-satisfying-tool-requirements)
  MUST be installed. Each pairs with a [`tooling.md`](tooling.md) entry and
  provides editor parity for the underlying CLI tool.
- Extensions in [`## Authoring Aids`](#authoring-aids) SHOULD be installed.
  These are recommended productivity extensions with no [`tooling.md`](tooling.md)
  counterpart.

## Extensions Satisfying Tool Requirements

### `markdownlint-cli2`

| Field             | Value                                                |
| ----------------- | ---------------------------------------------------- |
| Extension ID      | `DavidAnson.vscode-markdownlint`                     |
| Settings          | none                                                 |
| Reads config from | [`.markdownlint.json`](tooling.md#markdownlint-cli2) |

Provides real-time linting in the editor with diagnostics that mirror the
CLI's output exactly, because both surfaces resolve the same configuration
file at the repository root.

### `prettier`

| Field             | Value                                     |
| ----------------- | ----------------------------------------- |
| Extension ID      | `esbenp.prettier-vscode`                  |
| Settings          | see snippet below                         |
| Reads config from | [`.prettierrc.json`](tooling.md#prettier) |

```jsonc
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "[markdown]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true,
  },
}
```

`editor.formatOnSave` for markdown is intentional — it keeps authored content
consistently formatted without requiring an explicit CLI invocation.

## Authoring Aids

### `markdown-all-in-one`

| Field             | Value                        |
| ----------------- | ---------------------------- |
| Extension ID      | `yzhang.markdown-all-in-one` |
| Settings          | see snippet below            |
| Reads config from | N/A                          |

```jsonc
{
  "markdown.extension.toc.levels": "2..4",
}
```

Provides keyboard shortcuts, TOC generation, list editing, and preview
enhancements. The TOC level setting limits generated tables of contents to
H2 through H4, matching the heading hierarchy convention in
[`core.md`](core.md).

### `markdown-yaml-preamble`

| Field             | Value                            |
| ----------------- | -------------------------------- |
| Extension ID      | `bierner.markdown-yaml-preamble` |
| Settings          | see snippet below                |
| Reads config from | N/A                              |

```jsonc
{
  "markdown.preview.frontMatter": "table",
}
```

Renders YAML frontmatter in the markdown preview pane — relevant because the
project's markdown convention is to use YAML frontmatter for document
metadata (per [`core.md`](core.md)).

### `mermaid-chart`

| Field             | Value                               |
| ----------------- | ----------------------------------- |
| Extension ID      | `mermaidchart.vscode-mermaid-chart` |
| Settings          | none                                |
| Reads config from | N/A                                 |

Provides Mermaid diagram preview and editing, supporting the Mermaid usage
convention in [`core.md`](core.md).
