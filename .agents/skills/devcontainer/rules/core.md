# DevContainer Core Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Core requirements for DevContainer configurations including base image, lifecycle hooks, container configuration, and file structure.

## Principles

1. **Consistency** — Standardised configurations across all projects
2. **Security** — Non-root users, secure defaults, least privilege
3. **Performance** — Lightweight base images, optimised startup
4. **Developer Experience** — Essential tools included, clear documentation


## Base Image

### Mandatory Base Image

**ALL DevContainers MUST use:**

```docker
mcr.microsoft.com/devcontainers/base:ubuntu-24.04
```

This base image already includes:

- `common-utils` feature (with `vscode` user)
- `git` feature
- Zsh with Oh My Zsh

**Do NOT add these features.**

### Language Implementation

Use the base image with language-specific **features**:

```json
{
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu-24.04",
  "features": {
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.12"
    }
  }
}
```


## Container Configuration

### Required Settings

```jsonc
{
  "name": "[Project Name]",
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu-24.04",
  "remoteUser": "vscode",
  "privileged": false,
  "shutdownAction": "stopContainer",
  "init": true,
  "initializeCommand": "mkdir ${localEnv:HOME}${localEnv:USERPROFILE}/.devcontainer-extensions || echo ok",
  "postCreateCommand": "bash .devcontainer/post-create.sh --workspace-folder ${containerWorkspaceFolder}",
  "postStartCommand": "bash .devcontainer/post-start.sh --workspace-folder ${containerWorkspaceFolder}"
}
```

| Setting | Value | Purpose |
|---------|-------|---------|
| `remoteUser` | `"vscode"` | Non-root user for security |
| `privileged` | `false` | Disable privileged mode |
| `shutdownAction` | `"stopContainer"` | Clean container shutdown |
| `init` | `true` | Enable init process (tini) for proper signal handling |


## Lifecycle Hooks

### Initialize Command

The `initializeCommand` runs on the **host** before the container starts. Use it to prepare host-side resources needed by the container.

```json
{
  "initializeCommand": "mkdir ${localEnv:HOME}${localEnv:USERPROFILE}/.devcontainer-extensions || echo ok"
}
```

This creates the user-extension directory on the host, ensuring the bind mount target exists before the container starts. The `${localEnv:HOME}${localEnv:USERPROFILE}` pattern provides cross-platform compatibility (Linux/macOS uses `HOME`, Windows uses `USERPROFILE`).

### Post-Create Hook (Mandatory)

**ALWAYS** create `.devcontainer/post-create.sh`:

```json
{
  "postCreateCommand": "bash .devcontainer/post-create.sh --workspace-folder ${containerWorkspaceFolder}"
}
```

### Post-Start Hook (Mandatory)

**ALWAYS** create `.devcontainer/post-start.sh`:

```json
{
  "postStartCommand": "bash .devcontainer/post-start.sh --workspace-folder ${containerWorkspaceFolder}"
}
```

### Workspace Folder Argument

Lifecycle hooks **MUST** pass `--workspace-folder ${containerWorkspaceFolder}` to their scripts. This provides the container workspace path to scripts, enabling them to locate project files and `.devcontainer/` resources reliably regardless of how the container is launched.

### Script Execution

| Script | Purpose | Runs As |
|--------|---------|---------|
| `post-create.sh` | One-time setup after container creation | root |
| `post-start.sh` | Runs each time container starts | vscode user |


## Modular Script System

Lifecycle hooks use a modular `.d` directory pattern (similar to Linux init systems) for composable script execution.

### Directory Structure

```
.devcontainer/
├── devcontainer.json       # Main configuration (REQUIRED)
├── post-create.sh          # Orchestrator (REQUIRED)
├── post-start.sh           # Orchestrator (REQUIRED)
├── run-scripts.sh          # Shared runner library (REQUIRED)
├── post-create.d/          # Post-create scripts
│   └── 90-run-extensions-scripts.sh
└── post-start.d/           # Post-start scripts
    └── 90-run-extensions-scripts.sh
```

> Contributing rules loaded by the skill (e.g. `git-workflow/devcontainer.md`, language rules) may declare additional hooks. The skill assigns their filenames and numeric prefixes during assembly per `$DEVCONTAINER_SKILL/rules/assembly.md` § Lifecycle Script Filename Assignment.

### How It Works

1. `post-create.sh` and `post-start.sh` source `run-scripts.sh`
2. The runner scans the corresponding `.d` directory for `*.sh` files
3. Scripts execute in **sorted order** (numerical, then alphabetical)
4. Execution is **fail-fast** — first failure stops all subsequent scripts

### Adding Scripts

Create scripts in the appropriate `.d` directory with a numeric prefix for ordering:

```bash
# Example: .devcontainer/post-create.d/10-install-tools.sh
#!/bin/bash
set -euo pipefail

# Install project-specific tools
apt-get update && apt-get install -y jq
```

**Naming convention:** `NN-descriptive-name.sh` where `NN` is a two-digit number.

Scripts with the same prefix execute in alphabetical order (e.g., `10-aaa.sh` before `10-zzz.sh`).

### Disabling Scripts

To temporarily disable a script without deleting it, rename with `.skip.sh` suffix:

```bash
# Disable a script
mv 10-install-tools.sh 10-install-tools.skip.sh

# Re-enable
mv 10-install-tools.skip.sh 10-install-tools.sh
```

The runner ignores all `*.skip.sh` files.

### Script Requirements

Each script in a `.d` directory **MUST**:

1. Include shebang: `#!/bin/bash`
2. Enable strict mode: `set -euo pipefail`
3. Be executable or invoked via `bash`

### Orchestrator Contract

The orchestrator scripts (`post-create.sh`, `post-start.sh`) parse `--workspace-folder <path>` from their argv (passed by `postCreateCommand` / `postStartCommand` per § Required Settings) and **export `WORKSPACE_FOLDER` into the environment of every `.d/` script they run**.

Scripts in `.d/` directories:

- MAY rely on `${WORKSPACE_FOLDER}` being set to the container workspace path.
- MUST NOT reference `${containerWorkspaceFolder}` directly — that is a `devcontainer.json` substitution variable resolved at container-config time, not a runtime shell variable.
- MAY assume `WORKSPACE_FOLDER` is the only contractual variable provided by the orchestrator.

When a hook needs to apply configuration that should be visible to the dev user (`vscode`) regardless of which user runs the post-create hook, prefer system-scoped configuration (e.g. `git config --system ...`, `/etc/...` paths) over per-user configuration. This avoids ambiguity around the executing user and is the recommended pattern for git/system-level configuration declared by contributing rules.

### Mandatory Scripts

| Directory | Script | Purpose |
|-----------|--------|----------|
| `post-create.d/` | `90-run-extensions-scripts.sh` | Run user-specific post-create extension scripts |
| `post-start.d/` | `90-run-extensions-scripts.sh` | Run user-specific post-start extension scripts |

These are the only mandatory scripts owned by the skill. All other scripts in `.d/` directories are contributed by loaded `development-environment.md` rules and assembled per `$DEVCONTAINER_SKILL/rules/assembly.md`.

### User-Extension Hooks

The `90-run-extensions-scripts.sh` scripts in each `.d/` directory enable **user-specific customisation** without modifying the repository. They scan a bind-mounted `.devcontainer-extensions/` directory for user scripts.

**How it works:**

1. The `initializeCommand` creates `~/.devcontainer-extensions/` on the host
2. A bind mount maps this directory into the container at `${containerWorkspaceFolder}/.devcontainer-extensions/`
3. The `90-run-extensions-scripts.sh` scripts check for and execute scripts in the corresponding subdirectory

```
~/.devcontainer-extensions/          # Host directory (bind-mounted)
├── post-create.d/                   # User post-create scripts
│   └── 00-my-custom-setup.sh
└── post-start.d/                    # User post-start scripts
    └── 00-my-env-config.sh
```

The `90-` prefix ensures user-extension scripts run **after** all repository-defined scripts. User scripts follow the same naming convention (`NN-descriptive-name.sh`) and requirements (shebang, strict mode) as repository scripts.


## Mounts

DevContainers use bind mounts to share host resources with the container.

### Standard Mount Patterns

```jsonc
"mounts": [
  // User ssh keys
  "source=${localEnv:HOME}${localEnv:USERPROFILE}/.ssh,target=/home/vscode/.ssh,type=bind,readonly",

  // User specific lifecycle hooks scripts
  "source=${localEnv:HOME}${localEnv:USERPROFILE}/.devcontainer-extensions,target=${containerWorkspaceFolder}/.devcontainer-extensions,type=bind,consistency=cached"
]
```

| Mount | Purpose | Options |
|-------|---------|----------|
| SSH keys | Share host SSH keys for git authentication | `readonly` — container cannot modify host keys |
| User extensions | Bind user-specific lifecycle hook scripts | `consistency=cached` — optimised for read-heavy host-to-container sync |

Both mounts use `${localEnv:HOME}${localEnv:USERPROFILE}` for cross-platform host path resolution.


## File Structure

```
.devcontainer/
├── devcontainer.json                # Main configuration (REQUIRED)
├── post-create.sh                   # Post-create orchestrator (REQUIRED)
├── post-start.sh                    # Post-start orchestrator (REQUIRED)
├── run-scripts.sh                   # Shared runner library (REQUIRED)
├── post-create.d/                   # Post-create scripts (REQUIRED)
│   └── 90-run-extensions-scripts.sh
└── post-start.d/                    # Post-start scripts (REQUIRED)
    └── 90-run-extensions-scripts.sh
```

> The diagrams above show the skill-owned skeleton only. Contributing rules add their own scripts to the `.d/` directories; filenames and prefixes are assigned by the skill per `$DEVCONTAINER_SKILL/rules/assembly.md`.


## References

- [DevContainer Specification](https://containers.dev/implementors/spec/)
- [VS Code DevContainer Documentation](https://code.visualstudio.com/docs/devcontainers/containers)
- [Base Image Source](https://github.com/devcontainers/images/blob/main/src/base-ubuntu/.devcontainer/devcontainer.json)
