# Bash DevContainer Host Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

DevContainer is a host environment that owns the filesystem, runtimes, and CLI tools available for productive bash script authoring.

This rule is **prescriptive**: every tool listed below is a REQUIREMENT this host MUST provide. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

Lifecycle hook references in tool blocks resolve to named scripts in the project's devcontainer lifecycle hooks catalog or to `none`. Bash itself is part of the base image and is not declared as a runtime; both tools below are statically compiled binaries with no runtime dependency.

## Tools

### `shellcheck`

| Field          | Value                                               |
| -------------- | --------------------------------------------------- |
| Install        | `ghcr.io/devcontainers-extra/features/shellcheck:1` |
| Runtime        | none                                                |
| Lifecycle hook | none                                                |
| Config file    | [`.shellcheckrc`](tooling.md#shellcheck)            |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers-extra/features/shellcheck:1": {
      "version": "0.11.0",
    },
  },
}
```

### `shfmt`

| Field          | Value                                                |
| -------------- | ---------------------------------------------------- |
| Install        | `ghcr.io/devcontainers-extra/features/shfmt:1`       |
| Runtime        | none                                                 |
| Lifecycle hook | none                                                 |
| Config file    | [`.editorconfig`](tooling.md#shfmt) (`[*.sh]` block) |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers-extra/features/shfmt:1": {
      "version": "3.12.0",
    },
  },
}
```

The `shfmt` binary is pinned and placed on `PATH` explicitly so CLI and agent invocations have a deterministic version available without runtime resolution.
