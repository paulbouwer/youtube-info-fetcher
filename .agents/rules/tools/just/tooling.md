# Just Tooling Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

- This rule is **prescriptive**: every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide.
- A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.

## Tool Catalog

| Type           | Tool   | Purpose                                                                        |
| -------------- | ------ | ------------------------------------------------------------------------------ |
| Command runner | `just` | Execute project recipes for build, test, lint, format, and CI-equivalent flows |

## `just`

| Field       | Value                                                                                             |
| ----------- | ------------------------------------------------------------------------------------------------- |
| Purpose     | Run project recipes that orchestrate language toolchains and provide a consistent task interface. |
| Runtime     | none                                                                                              |
| Config file | none                                                                                              |

`just` uses its built-in defaults; no project configuration file is required.

The project's `justfile` at the repository root is the recipe content `just` executes, not tool configuration. Recipe authoring conventions, the required `justfile` header (`set shell`, `set dotenv-load`, `default` recipe), canonical recipe names, and naming policies are defined in [`core.md`](core.md). The `justfile` itself is a content artefact authored by the project, analogous to a source file rather than a tool config that tunes `just`'s own behaviour.
