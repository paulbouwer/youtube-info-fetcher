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

| Capability | Action | Description |
|------------|--------|-------------|
| <Verb> | `$<SKILL_NAME>_SKILL/actions/<action>.md` | <What this capability does> |

### Bundled Rules

| Rule | File | Description |
|------|------|-------------|
| Core | `$<SKILL_NAME>_SKILL/rules/core.md` | <What the rules authority covers> |
| Checklist | `$<SKILL_NAME>_SKILL/rules/checklist.md` | <What the validation checklist covers> |

### Bundled Templates

| Template | File | Description |
|----------|------|-------------|
| <Name> | `$<SKILL_NAME>_SKILL/templates/<template>.md` | <What the template produces> |

<!-- If the skill also references project rules from $AGENT_SYSTEM_FOLDER/rules/, add a brief sentence here noting that actions load appropriate project rules based on detected project context. -->

<!-- Add operational sections here if needed (e.g. Index Maintenance). Reference them by section name from the Flow's Execution Steps. -->

## Flow

### Prerequisites

- [ ] Verify <required directory> exists
- [ ] Verify <required file> is accessible

### Execution Steps

1. Load the bundled rules listed in the **Bundled Rules** section into context
2. Load the bundled templates listed in the **Bundled Templates** section into context
3. Determine the user's intent and select the matching capability from the **Capabilities** section
4. Execute the selected capability — rules and templates are already in context
<!-- 5. <Domain-specific post-action step, if any — reference the relevant operational section by name> -->
