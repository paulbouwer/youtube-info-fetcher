# Work Tracking VS Code Workbench Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

VS Code is a workbench that runs inside a host environment and provides an editor surface for productive work-tracking workflows.

This rule is **prescriptive**. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md). No editor extensions are declared as satisfying tool requirements for this domain.

No authoring-aid extensions are recommended for this domain either. Project rules (issue title and section structure, type-to-branch-type mapping, board status names, work-package frontmatter, sub-issue linking, acceptance criteria authoring) are enforced through the `gh` CLI and the agent surface, both of which can be invoked deterministically and inspected for compliance. Marketplace extensions for issue browsing, pull request review, project board visualisation, and similar concerns do not expose the integration points required to enforce these rules uniformly across users and agents, and are intentionally not installed.
