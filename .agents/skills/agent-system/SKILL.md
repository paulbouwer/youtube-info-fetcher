---
name: agent-system
description: Author and review agent-system assets — skills, actions, rules, checklists, AGENTS.md files, and per-domain rules families
---

# Agent System Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

This skill provides capabilities for creating, modifying, and reviewing agent-system assets — the skills, actions, rules, checklists, system-level `AGENTS.md` files, and per-domain rules families that define how the agent-system itself works. The bundled rules encode the invariants of the system; this skill ensures every authored asset stays compliant.

## Configuration

### Capabilities

| Capability | Action | Description |
|------------|--------|-------------|
| Author | `$AGENT_SYSTEM_SKILL/actions/author.md` | Create a new agent-system asset, or modify an existing one, by routing to the matching rule and template |
| Review | `$AGENT_SYSTEM_SKILL/actions/review.md` | Validate one or more agent-system assets against the bundled validation checklist |

### Bundled Rules

| Rule | File | Description |
|------|------|-------------|
| Rules Index | `$AGENT_SYSTEM_SKILL/rules/rules.index.md` | Lazy-loading index for this skill's rules; also declares which rules are non-overridable |
| Conventions | `$AGENT_SYSTEM_SKILL/rules/conventions.md` | Universal rules: variable system, formatting, cross-references, one-way visibility, composability, mutability contract, precedence model |
| Skill | `$AGENT_SYSTEM_SKILL/rules/skill.md` | `SKILL.md` manifest pattern |
| Action | `$AGENT_SYSTEM_SKILL/rules/action.md` | `actions/<action>.md` workflow pattern |
| Rule | `$AGENT_SYSTEM_SKILL/rules/rule.md` | Skill-bundled `rules/<topic>.md` authoring pattern |
| Checklist | `$AGENT_SYSTEM_SKILL/rules/checklist.md` | Skill-bundled validation checklist asset pattern |
| AGENTS.md | `$AGENT_SYSTEM_SKILL/rules/agents-md.md` | Root and agent-system `AGENTS.md` patterns |
| Rules Index | `$AGENT_SYSTEM_SKILL/rules/rules-index.md` | `rules.index.md` registry pattern (root category map + per-category file map) |
| Domain Core | `$AGENT_SYSTEM_SKILL/rules/domain-core.md` | Per-domain `<category>/<domain>/core.md` pattern |
| Domain Tooling | `$AGENT_SYSTEM_SKILL/rules/tooling.md` | Per-domain `tooling.md` pattern |
| Domain Development Environment | `$AGENT_SYSTEM_SKILL/rules/development-environment.md` | Per-domain `development-environment.md` index + `devcontainer.md` + `vscode.md` family pattern |
| Validation | `$AGENT_SYSTEM_SKILL/rules/validation.md` | Single validation checklist used by the Review action; sections grouped by asset type |

### Bundled Templates

| Template | File | Description |
|----------|------|-------------|
| Skill | `$AGENT_SYSTEM_SKILL/templates/skill.md` | `SKILL.md` skeleton |
| Action | `$AGENT_SYSTEM_SKILL/templates/action.md` | Action-file skeleton |
| Rule | `$AGENT_SYSTEM_SKILL/templates/rule.md` | Skill-bundled rule skeleton |
| Checklist | `$AGENT_SYSTEM_SKILL/templates/checklist.md` | Skill-bundled validation-checklist skeleton |
| AGENTS.md | `$AGENT_SYSTEM_SKILL/templates/agents-md.md` | Combined skeleton covering root and agent-system shapes |
| Rules Index | `$AGENT_SYSTEM_SKILL/templates/rules-index.md` | `rules.index.md` skeleton (root and per-category variants) |
| Domain Core | `$AGENT_SYSTEM_SKILL/templates/domain-core.md` | Per-domain `core.md` skeleton |
| Domain Tooling | `$AGENT_SYSTEM_SKILL/templates/tooling.md` | Per-domain `tooling.md` skeleton |
| Domain Development Environment | `$AGENT_SYSTEM_SKILL/templates/development-environment.md` | Per-domain dev-env index skeleton |
| DevContainer | `$AGENT_SYSTEM_SKILL/templates/devcontainer.md` | Per-domain `devcontainer.md` skeleton |
| VS Code | `$AGENT_SYSTEM_SKILL/templates/vscode.md` | Per-domain `vscode.md` skeleton |

This skill operates exclusively on agent-system assets and does not load project rules from `$AGENT_SYSTEM_FOLDER/rules/`. The bundled rules above are sufficient for every supported asset type.

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

1. Load the bundled rules listed in the **Bundled Rules** section into context
2. Load the bundled templates listed in the **Bundled Templates** section into context
3. Determine the user's intent and select the matching capability from the **Capabilities** section
4. Execute the selected capability — rules and templates are already in context
