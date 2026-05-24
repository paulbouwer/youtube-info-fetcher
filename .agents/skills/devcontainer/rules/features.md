# DevContainer Features Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Requirements for DevContainer features configuration and versioning.

## Versioning

Features use empty options `{}` by default, which installs the latest version at container build time. Specify an explicit version **only** when the project requires a particular version for correct operation.

```jsonc
// Good: Default — latest at build time
{
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {}
  }
}

// Good: Explicit version — when the project requires it
{
  "features": {
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.12"
    }
  }
}
```

| Scenario | Approach | Example |
|----------|----------|---------|
| Tooling features (linters, formatters, CLIs) | `{}` — latest is fine | `shellcheck`, `shfmt`, `github-cli` |
| Language runtimes with project constraints | Explicit version | `python:1` with `"version": "3.12"` |
| Infrastructure features | `{}` — latest is fine | `docker-in-docker`, `git-lfs` |

## Feature Sources

### Official Features

Prefer official DevContainer features from:

```
ghcr.io/devcontainers/features/<feature>:<major-version>
```

### Community Features

Community features from `ghcr.io/devcontainers-extra/features/` are acceptable when:

1. No official equivalent exists
2. Feature is well-maintained

## Excluded Features

The mandatory base image (`mcr.microsoft.com/devcontainers/base:ubuntu-24.04`) already includes:

- `common-utils` (provides `vscode` user)
- `git`

**Do NOT add `common-utils`** — it is redundant and may cause conflicts.

**Git feature exception:** Explicit git feature declaration is acceptable when following `rules/git-workflow/devcontainer.md`. This ensures consistency across different base images and documents the dependency explicitly.

## Recommended Features

### AI-Assisted Development

For environments that support AI-assisted development workflows:

```jsonc
"ghcr.io/devcontainers/features/copilot-cli:1": {}
```

The `copilot-cli` feature provides GitHub Copilot CLI tools for terminal-based AI assistance, enabling agent workflows that operate without VS Code.

## Feature Grouping

Features **SHOULD** be grouped by purpose with `// Group Name` single-line comments:

```jsonc
"features": {
  // Container development
  "ghcr.io/devcontainers/features/docker-in-docker:2": {},

  // Git, Git LFS
  "ghcr.io/devcontainers/features/git:1": {},
  "ghcr.io/devcontainers/features/git-lfs:1": {},

  // Coding agent CLIs
  "ghcr.io/devcontainers/features/copilot-cli:1": {}
}
```

## References

- [Official DevContainer Features](https://containers.dev/features)
- [Features Specification](https://containers.dev/implementors/features/)
