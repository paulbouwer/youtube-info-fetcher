---
name: devcontainer
description: Create and review DevContainer configurations
---

# DevContainer Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

This skill provides capabilities for creating and reviewing DevContainer configurations that comply with organisational rules.

## Configuration

### Capabilities

| Capability | Action | Description |
|------------|--------|-------------|
| Create | `$DEVCONTAINER_SKILL/actions/create.md` | Generate a rules-compliant DevContainer configuration |
| Review | `$DEVCONTAINER_SKILL/actions/review.md` | Analyse an existing DevContainer for compliance and improvements |

### Bundled Rules

| Rule | File | Description |
|----------|------|-------------|
| Rules Index | `$DEVCONTAINER_SKILL/rules/rules.index.md` | Lazy-loading index for this skill's rules |
| Core | `$DEVCONTAINER_SKILL/rules/core.md` | Base image, lifecycle hooks, container configuration, file structure |
| Extensions | `$DEVCONTAINER_SKILL/rules/extensions.md` | VS Code extension requirements and guidelines |
| Features | `$DEVCONTAINER_SKILL/rules/features.md` | DevContainer features configuration and versioning |
| Assembly | `$DEVCONTAINER_SKILL/rules/assembly.md` | Composition, dedup, conflict resolution, and lifecycle script filename assignment across loaded contributing rules |
| Security | `$DEVCONTAINER_SKILL/rules/security.md` | Security requirements and risk guidance |
| Checklist | `$DEVCONTAINER_SKILL/rules/checklist.md` | Consolidated compliance checklist |

### Bundled Templates

| Template | File | Description |
|----------|------|-------------|
| Base Configuration | `$DEVCONTAINER_SKILL/templates/devcontainer.json` | Base DevContainer configuration template |

Actions load additional project rules from `$AGENT_SYSTEM_FOLDER/rules/` based on the detected project context (languages, formats, provider).

## Flow

### Prerequisites

- [ ] Detect project language(s) from existing files or user specification
- [ ] Verify target repository structure is accessible

### Execution Steps

1. Load the bundled rules listed in the **Bundled Rules** section into context
2. Load the bundled templates listed in the **Bundled Templates** section into context
3. Determine the user's intent and select the matching capability from the **Capabilities** section
4. Execute the selected capability — bundled rules and templates are already in context; the action loads project rules from `$AGENT_SYSTEM_FOLDER/rules/` based on detected project context
