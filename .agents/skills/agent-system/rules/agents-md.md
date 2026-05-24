# AGENTS.md Files Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring the two `AGENTS.md` files in the system. Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`.

| File | Path | Purpose |
|------|------|---------|
| **Root `AGENTS.md`** | repository root | Repository entry point; defines root variables; describes the project system |
| **Agent-system `AGENTS.md`** | `$AGENT_SYSTEM_FOLDER/AGENTS.md` | Agent-system hub; inherits root variables; defines skill variables; catalogs all skills; documents Rules umbrella, mutability contract, precedence model |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/agents-md.md` | One template covers both shapes via section variants |
| **Discovery** | Both files are auto-discovered by AI tooling at their fixed paths; do not relocate |

## Root `AGENTS.md`

The repository entry point. Defines root variables and describes the project system. The first `AGENTS.md` an AI agent reads.

### Responsibilities

- Define `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, and any other repo-wide variables (e.g. `$REPO_PROVIDER`, `$WORK_TRACKING_PROVIDER`, `$WORK_TRACKING_PROJECT`)
- Describe the project folder structure and what each area is for (decision records, work packages, learnings, etc.)
- Point to `$AGENT_SYSTEM_FOLDER/AGENTS.md` as the agent-system entry point
- Reference skills through the agent-system entry point, not directly into skill internals

### Required Sections

| Section | Purpose |
|---------|---------|
| Title (project name) | Repository identity |
| `## Variables` | Definitions of all root variables |
| `## Project` | Brief project description |
| `## Agent System` | Pointer to `$AGENT_SYSTEM_FOLDER/AGENTS.md` |
| `## Project System` | Project folder structure and conventions |
| `## Git Workflow` | Pointer to git-workflow rules/skill |
| `## Work Tracking` | Pointer to work-tracking rules/skill |

### Rules

- Root `AGENTS.md` MUST NOT define skill variables — those belong in `$AGENT_SYSTEM_FOLDER/AGENTS.md`
- Root `AGENTS.md` MUST NOT enumerate skill internals (action names, rule filenames). Always go through the agent-system entry point.
- Variable definitions use `VARIABLE_NAME := value` format (no trailing slash)
- Use the "Rules" umbrella and the "skill rules" / "project rules" qualifiers in prose

## Agent-System `AGENTS.md`

The agent-system hub. Inherits root variables, defines skill variables, catalogs all skills. The second `AGENTS.md` an AI agent reads when working with the agent system.

### Responsibilities

- Inherit root variables in a `## Variables` block
- Define skill variables (`$<DOMAIN>_SKILL := $AGENT_SYSTEM_FOLDER/skills/<domain>`) in the same block
- Document philosophy (Specialisation, Isolation, Composability, Lazy Loading)
- Document system structure (folder tree showing `rules/` in both tiers and `rules.index.md` files)
- Explain how skills are invoked (slash command or keyword match)
- Maintain the Skill Catalog registry table
- Document the **Rules umbrella** — single concept, two tiers (skill rules and project rules) with the same folder name
- Document the **mutability contract** — skill rules immutable per project; project rules mutable
- Document the **precedence model** — project rules override skill rules by default; skills MAY mark specific rules non-overridable in their `rules.index.md`
- Describe how to load project and skill-bundled rules via the two-level `rules.index.md` lazy-loading pattern

### Required Sections

| Section | Purpose |
|---------|---------|
| Title (`# Agent System`) | Hub identity |
| `## Variables` | Inherits root + defines skill variables |
| `## Overview` | One-paragraph system description |
| `## Philosophy` | Bulleted principles (Specialisation, Isolation, Composability, Lazy Loading) |
| `## Structure` | Folder tree of `$AGENT_SYSTEM_FOLDER/` showing `rules/` in skills and at the project root, plus `rules.index.md` files |
| `## Skills` | Invocation explanation (slash command vs keyword match) |
| `## Skill Catalog` | Registry table of all skills |
| `## Rules` | Rules umbrella, two-tier model, mutability contract, precedence model, loading instructions (root + per-category `rules.index.md`) |

### Skill Catalog Table

```markdown
| Domain | Keywords | Skill | Actions |
|--------|----------|-------|---------|
| <Display name> | <comma-separated keywords> | `$<DOMAIN>_SKILL` | <comma-separated action names> |
```

- Domain — display name used in user-facing output
- Keywords — used by AI agents to match free-form requests to a skill
- Skill — the skill variable, not a bare path
- Actions — comma-separated list of action filenames without `.md` extension

### Rules

- Every skill listed in the Catalog MUST have a corresponding skill variable defined in the `## Variables` block
- Every skill variable defined MUST have a Catalog row — no orphan variables
- Skills are referenced by variable, never by bare path
- The `## Rules` section MUST document all four contracts (umbrella, two-tier model, mutability, precedence). Omitting any of them leaves consumers without the basis for resolving conflicts between skill rules and project rules.

## Cross-File Rules

- The two files together MUST resolve every variable used anywhere in the agent system. A variable referenced in a rule or skill that is not defined in one of these two files is non-conformant.
- Neither file defines a variable that belongs to a higher level (root never defines skill variables; agent-system never redefines root variables).
