# DevContainer Compliance Checklist

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Single-pass validation for a DevContainer configuration. Rules are defined across the bundled rules in `$DEVCONTAINER_SKILL/rules/`; this checklist provides a quick reference for verification.

## Configuration

Validate against `$DEVCONTAINER_SKILL/rules/core.md` § Base Image, § Container Configuration, and § File Structure:

- [ ] Uses mandatory base image (`mcr.microsoft.com/devcontainers/base:ubuntu-24.04`)
- [ ] Required container settings configured:
  - [ ] `remoteUser: "vscode"`
  - [ ] `privileged: false`
  - [ ] `shutdownAction: "stopContainer"`
  - [ ] `init: true`
- [ ] `.devcontainer/devcontainer.json` exists
- [ ] `.devcontainer/post-create.sh` exists
- [ ] `.devcontainer/post-start.sh` exists

## Features

Validate against `$DEVCONTAINER_SKILL/rules/features.md` § Versioning and § Excluded Features:

- [ ] Features use `{}` unless a specific version is required for project operation
- [ ] No redundant features (`common-utils`); git feature acceptable per `$DEVCONTAINER_SKILL/rules/features.md` § Excluded Features

## Extensions

Validate against `$DEVCONTAINER_SKILL/rules/extensions.md` § Essential Extensions and § Extension Guidelines:

- [ ] Essential extensions included
- [ ] All extensions bundled and documented with comments
- [ ] Language-specific extensions included per language rules
- [ ] Format-specific extensions included per format rules
- [ ] Markdown format extensions always included (essential format)

## Lifecycle Hooks

Validate against `$DEVCONTAINER_SKILL/rules/core.md` § Lifecycle Hooks and § Modular Script System:

- [ ] `post-create.sh` and `post-start.sh` exist and use modular `.d` directory pattern
- [ ] Uses `bash .devcontainer/*.sh --workspace-folder ${containerWorkspaceFolder}` execution pattern
- [ ] All hooks declared by loaded contributing rules are present in the assembled config and produce the expected filenames and prefixes per `$DEVCONTAINER_SKILL/rules/assembly.md` (verified by re-running assembly)

## Security

Validate against `$DEVCONTAINER_SKILL/rules/security.md`:

**Critical:**

- [ ] Non-root user configured (`remoteUser: "vscode"`)
- [ ] No `privileged: true` (unless documented justification)
- [ ] No hardcoded secrets or credentials

**High:**

- [ ] Host mounts are minimal and restricted
- [ ] Using mandatory base image

**Medium:**

- [ ] Port forwarding limited to development needs
- [ ] All forwarded ports have `portsAttributes` with descriptive labels

## Agent-Environment Validation

- [ ] Lifecycle hooks function without VS Code (terminal-only mode)
- [ ] CLI tools (features) available in terminal-only mode
- [ ] No hard dependency on VS Code extensions for core functionality
