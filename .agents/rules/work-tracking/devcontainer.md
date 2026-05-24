# Work Tracking DevContainer Host Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, and `$WORK_TRACKING_PROVIDER` from root `AGENTS.md`

## Overview

DevContainer is a host environment that owns the filesystem, runtimes, and CLI tools available for productive work-tracking workflows.

This rule is **prescriptive**: every tool listed below is a REQUIREMENT this host MUST provide. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

Lifecycle hook references in tool blocks resolve to named scripts in the project's devcontainer lifecycle hooks catalog or to `none`. The single tool in this domain is a statically distributed binary with no runtime dependency, so no Runtimes block is declared.

## Tools

### `gh`

| Field          | Value                                              |
| -------------- | -------------------------------------------------- |
| Install        | `ghcr.io/devcontainers/features/github-cli:1`      |
| Runtime        | none                                               |
| Lifecycle hook | none                                               |
| Config file    | [`gh`](tooling.md#gh)                              |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers/features/github-cli:1": {},
  },
}
```

`gh` is provisioned when `$WORK_TRACKING_PROVIDER = github`; see [`tooling.md`](tooling.md#gh) for the conditional rationale and the post-provisioning authentication contract. Authentication (`gh auth login`) is interactive and is the user's responsibility after the host environment is up; it is not declared as a lifecycle hook because it cannot run reliably in a non-interactive provisioning step.
