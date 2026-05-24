# Markdown Tooling Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

- This rule is **prescriptive**: every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide.
- A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.

## Tool Catalog

| Type      | Tool                | Purpose                                  |
| --------- | ------------------- | ---------------------------------------- |
| Formatter | `prettier`          | Format markdown for consistent style     |
| Linter    | `markdownlint-cli2` | Lint markdown files for rule conformance |

## `markdownlint-cli2`

| Field       | Value                                                                                                                                             |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Purpose     | Lint markdown files for conformance to the project's markdown rules — heading hierarchy, list indentation, table formatting, and stylistic drift. |
| Runtime     | Node ≥ 20                                                                                                                                         |
| Config file | `.markdownlint.json` at the repository root                                                                                                       |

The recommended configuration for `markdownlint-cli2` is:

```jsonc
{
  "default": true,
  "MD013": false,
  "MD007": { "indent": 2 },
  "MD010": { "code_blocks": true, "spaces_per_tab": 2 },
  "MD024": { "siblings_only": true },
}
```

| Rule      | Setting                                        | Rationale                                                                                                                                                                                                                         |
| --------- | ---------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `default` | `true`                                         | Enable all rules as a baseline, then override below.                                                                                                                                                                              |
| `MD013`   | `false`                                        | Disable line length enforcement — long lines acceptable in markdown (tables, links, prose).                                                                                                                                       |
| `MD007`   | `{ "indent": 2 }`                              | Enforce 2-space indentation for nested list items.                                                                                                                                                                                |
| `MD010`   | `{ "code_blocks": true, "spaces_per_tab": 2 }` | Convert tabs to 2 spaces in code blocks for consistent rendering.                                                                                                                                                                 |
| `MD024`   | `{ "siblings_only": true }`                    | Allow duplicate heading text where the parent heading differs — useful for documents that organise per-tool blocks under multiple top-level groupings (e.g. a tool name appearing under both a primary and a supporting section). |

Every installation of `markdownlint-cli2` (CLI, editor integrations,
future agent integrations) MUST resolve `.markdownlint.json` at the
repository root, so linting stays consistent regardless of where it
runs.

## `prettier`

| Field       | Value                                                                                                                              |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Purpose     | Format markdown files for consistent spacing, table column alignment, list marker normalisation, and trailing newline enforcement. |
| Runtime     | Node ≥ 20                                                                                                                          |
| Config file | `.prettierrc.json` at the repository root                                                                                          |

The recommended configuration for `prettier` is:

```json
{
  "tabWidth": 2,
  "useTabs": false,
  "proseWrap": "preserve"
}
```

| Setting     | Value        | Rationale                                                                                                                                             |
| ----------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tabWidth`  | `2`          | Aligns with `markdownlint-cli2`'s `MD007.indent` and `MD010.spaces_per_tab` so nested lists and code-block indentation never trigger linter findings. |
| `useTabs`   | `false`      | Spaces only, so `MD010` (no-hard-tabs) holds whenever `prettier` writes a file.                                                                       |
| `proseWrap` | `"preserve"` | Do not reflow paragraph prose. Authors choose where lines break; `MD013` is disabled on the linter side, so reflowing would be churn for no signal.   |

Every installation of `prettier` (CLI, editor integrations, future agent
integrations) MUST resolve `.prettierrc.json` at the repository root, so
formatting stays consistent regardless of where it runs.
