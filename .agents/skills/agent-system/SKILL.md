---
name: agent-system
description: Author and review agent-system assets — skills, actions, rules, checklists, AGENTS.md files, and per-domain rules families
---

# Agent System Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

This skill provides capabilities for creating, modifying, and reviewing agent-system assets — the skills, actions, rules, checklists, system-level `AGENTS.md` files, and per-domain rules families that define how the agent-system itself works. Its rules encode the invariants of the system; this skill ensures every authored asset stays compliant.

## Configuration

### Capabilities

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|
| Author | `$AGENT_SYSTEM_SKILL/actions/author.md` | Create a new agent-system asset, or modify an existing one, by routing to the matching rule and template | User wants to create or modify any agent-system asset (SKILL.md, action, rule, checklist, AGENTS.md, rules.index.md, or domain rule family) |
| Review | `$AGENT_SYSTEM_SKILL/actions/review.md` | Validate one or more agent-system assets against the bundled validation checklist | User wants to validate or review one or more existing agent-system assets |

### Rules

| Index | File |
|-------|------|
| Skill Rules Index | `$AGENT_SYSTEM_SKILL/rules/rules.index.md` |

Consult the index and load only the rule files whose **Load when** matches the selected capability and the asset type in scope. `conventions.md` is always loaded; `validation.md` loads for the Review capability.

### Bundled Templates

| Template | File | Load when |
|----------|------|-----------|
| Skill | `$AGENT_SYSTEM_SKILL/templates/skill.md` | Authoring a `SKILL.md` |
| Action | `$AGENT_SYSTEM_SKILL/templates/action.md` | Authoring an action file |
| Rule | `$AGENT_SYSTEM_SKILL/templates/rule.md` | Authoring a skill-bundled rule (`rules/<topic>.md`) |
| Checklist | `$AGENT_SYSTEM_SKILL/templates/checklist.md` | Authoring a `checklist.md` or `validation.md` |
| AGENTS.md | `$AGENT_SYSTEM_SKILL/templates/agents-md.md` | Authoring a root or agent-system `AGENTS.md` |
| Rules Index | `$AGENT_SYSTEM_SKILL/templates/rules-index.md` | Authoring a `rules.index.md` (root or per-category) |
| Domain Core | `$AGENT_SYSTEM_SKILL/templates/domain-core.md` | Authoring a domain `core.md` |
| Domain Tooling | `$AGENT_SYSTEM_SKILL/templates/tooling.md` | Authoring a domain `tooling.md` |
| Domain Development Environment | `$AGENT_SYSTEM_SKILL/templates/development-environment.md` | Authoring a domain `development-environment.md` |
| DevContainer | `$AGENT_SYSTEM_SKILL/templates/devcontainer.md` | Authoring a domain `devcontainer.md` |
| VS Code | `$AGENT_SYSTEM_SKILL/templates/vscode.md` | Authoring a domain `vscode.md` |

This skill operates exclusively on agent-system assets and does not load project rules from `$AGENT_SYSTEM_FOLDER/rules/`; it lists only the Skill Rules Index.

### Asset-Type Routing

The Author and Review actions both detect the asset type of each file they handle and load the matching rule plus (for Author) the matching template. The mapping is:

| Asset type | Rule | Template |
|------------|------|----------|
| `SKILL.md` | `skill.md` | `skill.md` |
| `actions/<name>.md` | `action.md` | `action.md` |
| `rules/<name>.md` (topic rule) | `rule.md` | `rule.md` |
| `rules/checklist.md` or `rules/validation.md` | `checklist.md` | `checklist.md` |
| Repository-root or `$AGENT_SYSTEM_FOLDER` `AGENTS.md` | `agents-md.md` | `agents-md.md` |
| `rules/rules.index.md` (root or per-category) | `rules-index.md` | `rules-index.md` |
| `rules/<category>/<domain>/core.md` | `domain-core.md` | `domain-core.md` |
| `rules/<category>/<domain>/tooling.md` | `tooling.md` | `tooling.md` |
| `rules/<category>/<domain>/development-environment.md` | `development-environment.md` | `development-environment.md` |
| `rules/<category>/<domain>/devcontainer.md` | `development-environment.md` | `devcontainer.md` |
| `rules/<category>/<domain>/vscode.md` | `development-environment.md` | `vscode.md` |

Universal rules from `conventions.md` apply to every asset type.

## Flow

### Prerequisites

- [ ] Verify `$AGENT_SYSTEM_SKILL/rules/` is accessible
- [ ] Verify `$AGENT_SYSTEM_SKILL/templates/` is accessible

### Execution Steps

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. For each asset in scope, detect its asset type (see **Asset-Type Routing**) and load via the **Rules** index only the matching rule — `conventions.md` is always loaded; `validation.md` loads for Review
3. For the Author capability, load the bundled template matching each asset type per **Asset-Type Routing**
4. Execute the selected capability
