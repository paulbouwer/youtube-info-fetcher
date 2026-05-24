# Skill Manifest Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL`, `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring a skill manifest at `$AGENT_SYSTEM_FOLDER/skills/<domain>/SKILL.md`. Universal rules (variables, formatting, cross-references) come from `$AGENT_SYSTEM_SKILL/rules/conventions.md` and are not restated here.

| Item | Rule |
|------|------|
| **Path** | `$AGENT_SYSTEM_FOLDER/skills/<domain>/SKILL.md` |
| **Domain folder** | `kebab-case`, lowercase, no abbreviations |
| **Frontmatter** | YAML, `name` and `description` required |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/skill.md` |
| **Reference implementation** | `$DECISION_RECORDS_SKILL/SKILL.md` |
| **Required H2 sections** | Variables, Purpose, Configuration, Flow |
| **Heading cap** | H3 |

## Purpose

A skill manifest is the orchestration layer. It owns rules loading, template loading, intent routing, and any cross-cutting operational concerns (like index maintenance). Actions never load bundled rules themselves — `SKILL.md` does it once, then routes.

## File Structure

| Section | Required | Purpose |
|---------|----------|---------|
| Frontmatter | Yes | `name`, `description` (used for keyword routing) |
| `## Variables` | Yes | Inheritance declaration |
| `## Purpose` | Yes | One-sentence skill description |
| `## Configuration` | Yes | Tables: Capabilities, Bundled Rules, Bundled Templates |
| Operational sections | If needed | Domain-specific concerns (e.g. Index Maintenance) |
| `## Flow` | Yes | Prerequisites checklist + Execution Steps |

### Config-First Rationale

Configuration sections appear *before* Flow intentionally. AI agents read documents top-to-bottom. When the Flow section references "the Capabilities table" or "bundled rules", those references must already exist in the agent's context. Forward references (mentioning something before it is defined) increase cognitive overhead and hallucination risk.

## Frontmatter

```yaml
---
name: <skill-name>
description: <one-sentence what the skill does>
---
```

- `name` — kebab-case; matches the folder name
- `description` — used by AI agents for keyword/intent matching; phrase as an action ("Create and review …", "Execute …")
- Other fields (`license`, `allowed-tools`) are optional per the Agent Skills open standard

## Configuration Tables

Three tables in fixed order:

```markdown
### Capabilities

| Capability | Action | Description |
|------------|--------|-------------|

### Bundled Rules

| Rule | File | Description |
|------|------|-------------|

### Bundled Templates

| Template | File | Description |
|----------|------|-------------|
```

All paths in tables MUST use skill variables: `$<SKILL>_SKILL/actions/create.md`.

When a skill also references project rules from `$AGENT_SYSTEM_FOLDER/rules/` (e.g. language, format, or provider rules), add a brief sentence after the Bundled Templates table noting that actions load appropriate project rules based on detected project context. The actions own this loading — the skill manifest does not enumerate or table-list project rules.

## Flow Pattern

```markdown
### Prerequisites

- [ ] Verify required directories exist
- [ ] Verify required files are accessible

### Execution Steps

1. Load the bundled rules listed in the **Bundled Rules** section into context
2. Load the bundled templates listed in the **Bundled Templates** section into context
3. Determine the user's intent and select the matching capability from the **Capabilities** section
4. Execute the selected capability — rules and templates are already in context
5. [Domain-specific post-action step, if any — reference the relevant operational section by name]
```

## Rules

- The skill owns all bundled rules and template loading — actions never load bundled rules themselves
- Actions own project-rules loading — they determine which rules from `$AGENT_SYSTEM_FOLDER/rules/` to load based on detected project context (languages, formats, provider)
- Operational concerns (index maintenance, file moves, post-action notifications) are aggregated in the skill manifest, not scattered across actions
- Execution-step references to other sections within the same file use the section name (e.g. "as per the **Index Maintenance** section")
- Heading depth is capped at H3
