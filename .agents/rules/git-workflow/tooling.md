# Git Workflow Tooling Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, and `$REPO_PROVIDER` from root `AGENTS.md`

## Overview

- This rule is **prescriptive**: every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide.
- A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.
- Project-level git behaviour MUST be applied at the system scope by host lifecycle hooks rather than relying on user-level (`~/.gitconfig`) configuration, so every user and agent observes identical behaviour regardless of their personal git settings.

## Tool Catalog

| Type                       | Tool       | Purpose                                                                 |
| -------------------------- | ---------- | ----------------------------------------------------------------------- |
| Version control            | `git`      | Source control client used for branching, committing, and signing       |
| Version control extension  | `git-lfs`  | Large-file storage support layered on git                               |
| Repo provider CLI          | `gh`       | GitHub CLI used to interact with pull requests and releases             |

## `git`

| Field       | Value |
| ----------- | ----- |
| Purpose     | Provide source control for branching, committing, signing, and history navigation. |
| Runtime     | none  |
| Config file | none  |

`git` uses its built-in defaults augmented by system-scope settings applied by host lifecycle hooks; no project configuration file is required.

Project-level git behaviour (workspace safe-directory marking, allowed-signers configuration for SSH-signed commits and tags) is applied at the system scope by host lifecycle hooks so it takes effect regardless of which user runs `git` inside the host. Repository-tracked files such as `.gitattributes` and `.gitignore` carry content rules (line-ending normalisation, ignore patterns, LFS filter declarations) and are not tool configuration in the sense of this rule. User-level signing configuration (`~/.gitconfig`'s `commit.gpgsign`, `gpg.format = ssh`, `user.signingkey`) remains the user's responsibility and is not declared here.

## `git-lfs`

| Field       | Value |
| ----------- | ----- |
| Purpose     | Extend git with efficient handling of large binary files via the LFS protocol. |
| Runtime     | none  |
| Config file | none  |

`git-lfs` uses its built-in defaults; no project configuration file is required.

`git-lfs` is included unconditionally so that a contributor who later adds LFS-tracked content does not need to reprovision their environment to obtain the binary.

## `gh`

| Field       | Value |
| ----------- | ----- |
| Purpose     | Interact with the configured repository provider for pull requests and releases from the command line. |
| Runtime     | none  |
| Config file | none  |

`gh` uses its built-in defaults; no project configuration file is required.

`gh` is the concrete tool selected when `$REPO_PROVIDER = github`. If a future project sets `$REPO_PROVIDER` to a different provider, this entry would be replaced by the corresponding provider's CLI rather than abstracted preemptively. Authentication (`gh auth login`) is interactive and is the user's responsibility after the host environment is provisioned; it is not a project-level configurable artefact.
