---
name: <skill-name>
description: <one-sentence description, action-phrased — used for keyword/intent matching>
---

# <Skill Display Name> Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$<SKILL_NAME>_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

<One-sentence description of what this skill does and the value it provides.>

## Configuration

### Capabilities

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|
| <Verb> | `$<SKILL_NAME>_SKILL/actions/<action>.md` | <What this capability does> | <Intent trigger that routes here> |

### Rules

| Index | File |
|-------|------|
| Skill Rules Index | `$<SKILL_NAME>_SKILL/rules/rules.index.md` |
<!-- Add a Project Rules Index row ONLY if this skill maps 1:1 to a project-rules domain:
| Project Rules Index | `$AGENT_SYSTEM_FOLDER/rules/<domain>/rules.index.md` | -->

Consult each index and load only the rule files whose **Load when** matches the selected capability.

### Bundled Templates

<!-- Omit this entire section if the skill does not bundle templates. -->

| Template | File | Load when |
|----------|------|-----------|
| <Name> | `$<SKILL_NAME>_SKILL/templates/<template>.md` | <Intent trigger for loading this template> |

<!-- If the skill also references project rules from $AGENT_SYSTEM_FOLDER/rules/, add a brief sentence here noting that actions load appropriate project rules based on detected project context. -->

<!-- Add operational sections here if needed (e.g. Index Maintenance). Reference them by section name from the Flow's Execution Steps. -->

## Flow

### Prerequisites

- [ ] Verify <required directory> exists
- [ ] Verify <required file> is accessible

### Execution Steps

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. Load skill rules — and project rules only when a **Project Rules Index** is listed — via the indexes in the **Rules** section, loading only the rules whose **Load when** matches the selected capability
3. Load bundled templates whose **Load when** matches the selected capability
4. Execute the selected capability
<!-- 5. <Domain-specific post-action step, if any — reference the relevant operational section by name> -->
