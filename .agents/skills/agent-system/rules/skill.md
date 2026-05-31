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

A skill manifest is the orchestration layer. It owns intent routing, lazy rules and template loading, and any cross-cutting operational concerns (like index maintenance). It honours the system's lazy-loading philosophy: it routes intent to a capability **first**, then loads only the rules and templates whose `Load when` matches that capability — never all bundled rules up front. Actions never load bundled rules themselves — `SKILL.md` performs the capability-scoped selection, then routes.

## File Structure

| Section | Required | Purpose |
|---------|----------|---------|
| Frontmatter | Yes | `name`, `description` (used for keyword routing) |
| `## Variables` | Yes | Inheritance declaration |
| `## Purpose` | Yes | One-sentence skill description |
| `## Configuration` | Yes | Tables: Capabilities (with `Load when`), Rules (index-pointer), Bundled Templates (with `Load when`, only if the skill bundles templates) |
| Operational sections | If needed | Domain-specific concerns (e.g. Index Maintenance) |
| `## Flow` | Yes | Prerequisites checklist + Execution Steps |

### Config-First Rationale

Configuration sections appear *before* Flow intentionally. AI agents read documents top-to-bottom. When the Flow section references "the Capabilities table" or "the Rules section", those references must already exist in the agent's context. Forward references (mentioning something before it is defined) increase cognitive overhead and hallucination risk. The Configuration tables are routing metadata — reading them is cheap; loading the rule and template *files* they point at is deferred until a capability is selected (see Flow Pattern).

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

Capabilities and Rules are required. Bundled Templates appears only when the skill bundles skill-level templates.

```markdown
### Capabilities

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|

### Rules

| Index | File |
|-------|------|

### Bundled Templates

| Template | File | Load when |
|----------|------|-----------|
```

The **Capabilities** table MUST carry a `Load when` column — a concrete intent trigger per capability used to route the user's request to a capability.

The **Rules** table is a lean index-pointer, not an enumeration of every rule file. It lists the *Skill Rules Index* (`$<SKILL>_SKILL/rules/rules.index.md`) and, where the skill maps 1:1 to a project-rules domain, the *Project Rules Index* (`$AGENT_SYSTEM_FOLDER/rules/<domain>/rules.index.md`). Immediately after the table, include the instruction:

> Consult each index and load only the rule files whose **Load when** matches the selected capability.

The **Bundled Templates** table, when present, MUST carry a `Load when` column so templates are loaded per-capability rather than all at once.

All paths in tables MUST use skill variables: `$<SKILL>_SKILL/actions/create.md`.

When a skill references project rules from `$AGENT_SYSTEM_FOLDER/rules/` but does not map 1:1 to a single project-rules domain (e.g. it loads language, format, or provider rules by detected context), omit the Project Rules Index row and instead add a brief sentence after the tables noting that actions load appropriate project rules based on detected project context. The actions own this loading — the skill manifest does not enumerate or table-list project rules.

## Flow Pattern

```markdown
### Prerequisites

- [ ] Verify required directories exist
- [ ] Verify required files are accessible

### Execution Steps

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. Load skill rules — and project rules only when a **Project Rules Index** is listed — via the indexes in the **Rules** section, loading only the rules whose **Load when** matches the selected capability
3. Load bundled templates whose **Load when** matches the selected capability
4. Execute the selected capability
5. [Domain-specific post-action step, if any — reference the relevant operational section by name]
```

## Rules

- The skill owns lazy rules and template loading — it routes intent to a capability first, then loads only the rules and templates whose `Load when` matches that capability. Actions never load bundled rules themselves
- Actions own project-rules loading — they determine which rules from `$AGENT_SYSTEM_FOLDER/rules/` to load based on detected project context (languages, formats, provider)
- Operational concerns (index maintenance, file moves, post-action notifications) are aggregated in the skill manifest, not scattered across actions
- Execution-step references to other sections within the same file use the section name (e.g. "as per the **Index Maintenance** section")
- Heading depth is capped at H3
