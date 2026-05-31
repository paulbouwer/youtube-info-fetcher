# <Action Display Name>

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$<SKILL_NAME>_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

<One-sentence description of what this action does.>

## Flow

### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

<!-- If this action loads project rules (languages, formats, tools), declare the detection step here:
**Detect project context.** Inspect the repository to determine which project rules apply (e.g. read `$AGENT_SYSTEM_FOLDER/AGENTS.md` for `$REPO_PROVIDER`). Load the matching rules from `$AGENT_SYSTEM_FOLDER/rules/`. Honour precedence per `$AGENT_SYSTEM_SKILL/rules/conventions.md § Precedence Model`.
-->

### Step 1: <Verb Phrase>

**Goal:** <One sentence describing the step's outcome.>

<Step content. Use tables for structured choices, bullet lists for sequential sub-actions.>

### Step 2: <Verb Phrase>

**Goal:** <One sentence.>

<Step content.>

<!-- Add more steps as needed. Each step starts with **Goal:**. -->

## Error Handling

<!-- Drop this section if there are no error scenarios worth documenting. -->

| Scenario | Action |
|----------|--------|
| <Trigger> | <What to do> |
| <Trigger> | <What to do> |
