# Git Workflow DevContainer Host Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, and `$REPO_PROVIDER` from root `AGENTS.md`

## Overview

DevContainer is a host environment that owns the filesystem, runtimes, and CLI tools available for productive git-based workflows.

This rule is **prescriptive**: every tool listed below is a REQUIREMENT this host MUST provide. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

Lifecycle hook references in tool blocks resolve to named scripts in the project's devcontainer lifecycle hooks catalog or to `none`. All tools in this domain are statically distributed binaries with no runtime dependency, so no Runtimes block is declared.

## Tools

### `git`

| Field          | Value                                                                                                                |
| -------------- | -------------------------------------------------------------------------------------------------------------------- |
| Install        | `ghcr.io/devcontainers/features/git:1`                                                                               |
| Runtime        | none                                                                                                                 |
| Lifecycle hook | `00-git-safe-directory.sh` (post-create), `50-ssh-allowed-signers.sh` (post-start)                                   |
| Config file    | [`git`](tooling.md#git)                                                                                              |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers/features/git:1": {},
  },
}
```

The `00-git-safe-directory.sh` post-create hook marks the workspace as a git safe directory at system scope, required when the container's user differs from the workspace owner so git commands operate against the workspace without trust errors. The `50-ssh-allowed-signers.sh` post-start hook configures `gpg.ssh.allowedSignersFile` at system scope so SSH-signed commits and tags can be verified locally; users populate the allowed-signers file themselves with entries identifying their own signing keys.

### `git-lfs`

| Field          | Value                                          |
| -------------- | ---------------------------------------------- |
| Install        | `ghcr.io/devcontainers/features/git-lfs:1`     |
| Runtime        | none                                           |
| Lifecycle hook | none                                           |
| Config file    | [`git-lfs`](tooling.md#git-lfs)                |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers/features/git-lfs:1": {},
  },
}
```

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

`gh` is provisioned when `$REPO_PROVIDER = github`; see [`tooling.md`](tooling.md#gh) for the conditional rationale and the post-provisioning authentication contract. Authentication (`gh auth login`) is interactive and is the user's responsibility after the host environment is up; it is not declared as a lifecycle hook because it cannot run reliably in a non-interactive provisioning step.
