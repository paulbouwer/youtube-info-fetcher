# Git Workflow VS Code Workbench Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

VS Code is a workbench that runs inside a host environment and provides an editor surface for productive git-based workflows.

This rule is **prescriptive**. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md). Extensions in [`## Extensions Satisfying Tool Requirements`](#extensions-satisfying-tool-requirements) MUST be installed; each pairs with a [`tooling.md`](tooling.md) entry and provides editor parity for the underlying CLI tool.

No authoring-aid extensions are recommended for this domain. Project rules (commit format, branch naming, pull request structure, AI attribution trailers, signing policy) are enforced through CLIs and the agent surface, both of which can be invoked deterministically and inspected for compliance. Marketplace extensions for source control, pull request review, history visualisation, and similar concerns do not expose the integration points required to enforce these rules uniformly across users and agents, and are intentionally not installed.

## Extensions Satisfying Tool Requirements

### `git`

| Field             | Value                                |
| ----------------- | ------------------------------------ |
| Extension ID      | `vscode.git` (built-in)              |
| Settings          | none                                 |
| Reads config from | [`git`](tooling.md#git)              |

Provides the source-control panel, diff viewer, and inline blame UI. Operates against the `git` binary on PATH installed by the host environment and observes the system-scope configuration applied by the host's git lifecycle hooks (safe-directory, allowed signers) without additional editor-side configuration. As a built-in extension, no install action is required in the workbench.
