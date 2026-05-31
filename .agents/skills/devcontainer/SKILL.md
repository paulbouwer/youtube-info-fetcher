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

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|
| Create | `$DEVCONTAINER_SKILL/actions/create.md` | Generate a rules-compliant DevContainer configuration | User wants to create a new DevContainer configuration |
| Review | `$DEVCONTAINER_SKILL/actions/review.md` | Analyse an existing DevContainer for compliance and improvements | User wants to review or improve an existing DevContainer configuration |

### Rules

| Index | File |
|-------|------|
| Skill Rules Index | `$DEVCONTAINER_SKILL/rules/rules.index.md` |

Consult each index and load only the rule files whose **Load when** matches the selected capability.

### Bundled Templates

| Template | File | Load when |
|----------|------|-----------|
| Base Configuration | `$DEVCONTAINER_SKILL/templates/devcontainer.json` | Running the Create capability (generating a new configuration) |

Actions load additional project rules from `$AGENT_SYSTEM_FOLDER/rules/` based on the detected project context (languages, formats, provider).

## Flow

### Prerequisites

- [ ] Detect project language(s) from existing files or user specification
- [ ] Verify target repository structure is accessible

### Execution Steps

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. Load skill rules via the **Rules** index — only the rules whose **Load when** matches the selected capability
3. Load bundled templates whose **Load when** matches the selected capability
4. Execute the selected capability — the action loads project rules from `$AGENT_SYSTEM_FOLDER/rules/` based on detected project context
