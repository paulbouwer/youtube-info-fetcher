# <Domain> Tooling Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

- This rule is **prescriptive**: every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide.
- A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.

## Tool Catalog

| Type      | Tool          | Purpose                              |
| --------- | ------------- | ------------------------------------ |
| <Kind>    | `<tool-name>` | <One-phrase purpose in this domain>  |
| <Kind>    | `<tool-name>` | <One-phrase purpose in this domain>  |

## `<tool-name>`

| Field       | Value                                                                          |
| ----------- | ------------------------------------------------------------------------------ |
| Purpose     | <One-sentence statement of what this tool does in this domain.>                |
| Runtime     | <`Node ≥ 20`, `Go ≥ 1.24`, or `none` for natively compiled binaries>           |
| Config file | <`<filename>` at the repository root, or `none`>                               |

The recommended configuration for `<tool-name>` is:

```<lang>
<recommended config>
```

| Setting    | Value     | Rationale                                                            |
| ---------- | --------- | -------------------------------------------------------------------- |
| `<key>`    | `<value>` | <What this setting does and why; domain-agnostic justification>      |
| `<key>`    | `<value>` | <Rationale>                                                          |

Every installation of `<tool-name>` (CLI, editor integrations, future agent integrations) MUST resolve `<config>` at the repository root, so <linting/formatting/…> stays consistent regardless of where it runs.

<!-- For tools with no config, replace the configuration section with a single sentence:
`<tool-name>` uses its built-in defaults; no project configuration is required.
-->

## `<tool-name-2>`

<!-- Same skeleton repeated. Tools are flat at H2 — no kind-grouping wrappers. -->

<!-- Optional: closing `## <X> Alignment` paragraph if the formatter and linter operate on disjoint axes (file-level alignment).

## Dialect Alignment

<File-level alignment paragraph stating how the tools agree on dialect/target/encoding.>
-->
