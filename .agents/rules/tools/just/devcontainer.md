# Just DevContainer Host Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

DevContainer is a host environment that owns the filesystem, runtimes, and CLI tools available for productive use of the project's command runner.

This rule is **prescriptive**: every tool listed below is a REQUIREMENT this host MUST provide. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

Lifecycle hook references in tool blocks resolve to named scripts in the project's devcontainer lifecycle hooks catalog or to `none`. The tool in this domain is a statically distributed binary with no runtime dependency, so no Runtimes block is declared.

## Tools

### `just`

| Field          | Value                             |
| -------------- | --------------------------------- |
| Install        | `ghcr.io/guiyomh/features/just:0` |
| Runtime        | none                              |
| Lifecycle hook | `just-completions` (post-create)  |
| Config file    | [`just`](tooling.md#just)         |

```jsonc
{
  "features": {
    "ghcr.io/guiyomh/features/just:0": {
      "version": "1.50.0",
    },
  },
}
```

The `just-completions` post-create hook installs bash tab-completion for `just` so interactive shells in the host expose recipe names and flags via the standard completion mechanism. Completion is a workbench-CLI ergonomic and does not affect recipe execution.
