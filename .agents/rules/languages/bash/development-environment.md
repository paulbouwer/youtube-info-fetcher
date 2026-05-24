# Bash Development Environment Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

This rule is the **index** for the development environments from which a human user or an AI agent can productively author bash scripts for the project. It declares the cross-environment tool requirements and routes to per-environment files for the satisfaction details.

The model is two-layer:

- **Host Environments** — execution environments that own the filesystem, runtimes, and CLI tools (DevContainer today; future agent sandboxes / isolated execution environments).
- **Workbenches** — user- or agent-facing surfaces that run inside a host environment (VS Code today; future agent CLIs).

The rule is **prescriptive**: every requirement below MUST be satisfied by the listed environments. Tool versions, config schemas, and invocation patterns are NOT restated here — they live in [`tooling.md`](tooling.md) and are referenced by tool name only. Per-environment installation and integration mechanics live in the per-environment files linked below.

Not every [`tooling.md`](tooling.md) tool need appear in every per-environment file; a tool may be CLI-only or extension-only. Each per-environment file declares what it provides, and [`tooling.md`](tooling.md) remains the authoritative tool catalogue.

## Tool Requirements

Every host environment MUST provide the following tools:

- [`shellcheck`](tooling.md#shellcheck) — shell script linter
- [`shfmt`](tooling.md#shfmt) — shell script formatter

## Host Environments

- [DevContainer](devcontainer.md)

## Workbenches

- [VS Code](vscode.md)
