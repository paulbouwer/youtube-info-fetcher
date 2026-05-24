# Work Tracking Tooling Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, and `$WORK_TRACKING_PROVIDER` from root `AGENTS.md`

## Overview

- This rule is **prescriptive**: every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide.
- A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.

## Tool Catalog

| Type                          | Tool | Purpose                                                                                       |
| ----------------------------- | ---- | --------------------------------------------------------------------------------------------- |
| Work-tracking provider CLI    | `gh` | Work-tracking provider CLI for issues, sub-issues, projects, and board status updates         |

## `gh`

| Field       | Value |
| ----------- | ----- |
| Purpose     | Interact with the configured work-tracking provider for issues, sub-issues, projects, and board status updates from the command line. |
| Runtime     | none  |
| Config file | none  |

`gh` uses its built-in defaults; no project configuration file is required.

`gh` is the concrete tool selected when `$WORK_TRACKING_PROVIDER = github`. If a future project sets `$WORK_TRACKING_PROVIDER` to a different provider, this entry would be replaced by the corresponding provider's CLI rather than abstracted preemptively. `gh` ships native support for GitHub Projects v2 (`gh project ...`); no additional extension is required for board placement and status updates. Authentication (`gh auth login`) is interactive and is the user's responsibility after the host environment is provisioned; it is not a project-level configurable artefact.
