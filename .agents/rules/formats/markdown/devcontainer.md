# Markdown DevContainer Host Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

DevContainer is a host environment that owns the filesystem, runtimes, and CLI
tools available for productive markdown authoring.

This rule is **prescriptive**: every runtime and tool listed below is a
REQUIREMENT this host MUST provide. Tool requirements are catalogued in
[`tooling.md`](tooling.md); the cross-environment satisfaction map is in
[`development-environment.md`](development-environment.md).

Runtimes are declared once in [`## Runtimes`](#runtimes) and referenced by
every tool that needs them. Lifecycle hook references in tool blocks resolve
to named scripts in the project's devcontainer lifecycle hooks catalog or to
`none`.

## Runtimes

### `Node`

| Field   | Value                                   |
| ------- | --------------------------------------- |
| Version | 22                                |
| Feature | `ghcr.io/devcontainers/features/node:1` |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers/features/node:1": {
      "version": "22",
    },
  },
}
```

## Tools

### `markdownlint-cli2`

| Field          | Value                                                      |
| -------------- | ---------------------------------------------------------- |
| Install        | `ghcr.io/devcontainers-extra/features/markdownlint-cli2:1` |
| Runtime        | [`Node`](#node)                                            |
| Lifecycle hook | none                                                       |
| Config file    | [`.markdownlint.json`](tooling.md#markdownlint-cli2)       |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers-extra/features/markdownlint-cli2:1": {},
  },
}
```

The community feature also installs Node transitively as a side effect, but
Node MUST be declared explicitly in [`## Runtimes`](#runtimes) so its version
is the source of truth and is shared with other tools (e.g. `prettier`).

### `prettier`

| Field          | Value                                                                                   |
| -------------- | --------------------------------------------------------------------------------------- |
| Install        | `ghcr.io/devcontainers-extra/features/npm-package:1` (with `package: "prettier@3.3.3"`) |
| Runtime        | [`Node`](#node)                                                                         |
| Lifecycle hook | none                                                                                    |
| Config file    | [`.prettierrc.json`](tooling.md#prettier)                                               |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers-extra/features/npm-package:1": {
      "package": "prettier@3.3.3",
    },
  },
}
```

The `prettier` binary is pinned and placed on `PATH` explicitly rather than
being resolved on demand by `npx`, so CLI and agent invocations have a
deterministic version available without runtime resolution.
