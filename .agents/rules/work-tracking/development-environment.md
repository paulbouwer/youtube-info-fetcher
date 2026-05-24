# Work Tracking Development Environment Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, and `$WORK_TRACKING_PROVIDER` from root `AGENTS.md`

## Overview

This rule is the **index** for the development environments from which a human user or an AI agent can productively perform work-tracking workflows for the project. It declares the cross-environment tool requirements and routes to per-environment files for the satisfaction details.

The model is two-layer:

- **Host Environments** — execution environments that own the filesystem, runtimes, and CLI tools (DevContainer today; future agent sandboxes / isolated execution environments).
- **Workbenches** — user- or agent-facing surfaces that run inside a host environment (VS Code today; future agent CLIs).

The rule is **prescriptive**: every requirement below MUST be satisfied by the listed environments. Tool versions, config schemas, and invocation patterns are NOT restated here — they live in [`tooling.md`](tooling.md) and are referenced by tool name only. Per-environment installation and integration mechanics live in the per-environment files linked below.

Not every [`tooling.md`](tooling.md) tool need appear in every per-environment file; a tool may be CLI-only or extension-only. Each per-environment file declares what it provides, and [`tooling.md`](tooling.md) remains the authoritative tool catalogue.

## Tool Requirements

Every host environment MUST provide the following tools:

- [`gh`](tooling.md#gh) — work-tracking provider CLI for issue, sub-issue, project, and board workflows (when `$WORK_TRACKING_PROVIDER = github`)

## Host Environments

- [DevContainer](devcontainer.md)

## Workbenches

- [VS Code](vscode.md)
