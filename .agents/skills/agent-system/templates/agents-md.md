<!--
Template covers BOTH AGENTS.md shapes via section variants below.
Pick the section block that matches the file you are authoring; delete the other.
-->

# <Project Name | Agent System>

## Variables

<!-- ROOT AGENTS.md form: -->
- `AGENT_SYSTEM_FOLDER := .agents`
- `PROJECT_FOLDER := project`
- `REPO_PROVIDER := github`
- `WORK_TRACKING_PROVIDER := github`
- `WORK_TRACKING_PROJECT := <Project Board Name>`

<!-- AGENT-SYSTEM AGENTS.md form: -->
<!--
- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- `AGENT_SYSTEM_SKILL := $AGENT_SYSTEM_FOLDER/skills/agent-system`
- `<DOMAIN>_SKILL := $AGENT_SYSTEM_FOLDER/skills/<domain>`
-->

<!-- =====================================================================
ROOT AGENTS.md sections — use this block for the repository-root file
===================================================================== -->

## Project

<One-paragraph project description.>

## Agent System

This repository uses the agent-system for AI-assisted engineering.

- **Consult:** `$AGENT_SYSTEM_FOLDER/AGENTS.md` for available skills and rules.

## Project System

<Project folder structure and conventions. List decision-records, work, learnings, etc.>

## Git Workflow

This repository uses `$REPO_PROVIDER` as its repository provider.

- **Consult:** `$AGENT_SYSTEM_FOLDER/rules/git-workflow/` for branch naming, commit format, and PR structure project rules.

## Work Tracking

This repository uses `$WORK_TRACKING_PROVIDER` as its work tracking provider, with issues organised on the `$WORK_TRACKING_PROJECT` board.

- **Consult:** `$AGENT_SYSTEM_FOLDER/rules/work-tracking/` for issue, work-package, template, and development-environment project rules.

<!-- =====================================================================
AGENT-SYSTEM AGENTS.md sections — use this block for $AGENT_SYSTEM_FOLDER/AGENTS.md
===================================================================== -->

<!--
## Overview

This agent system provides a structured approach to AI-assisted development through skills and rules. It is designed to be composable — load only what is relevant to the task at hand.

## Philosophy

- **Specialisation** — Each skill focuses on a specific domain, providing deep expertise
- **Isolation** — Sessions operate with focused context, reducing noise and improving accuracy
- **Composability** — Skills and rules can be mixed and matched per session as needed
- **Lazy Loading** — Load only the skills and rules relevant to the current task

## Structure

```bash
$AGENT_SYSTEM_FOLDER/
├── AGENTS.md
├── skills/
│   └── <skill>/
│       ├── SKILL.md
│       ├── actions/
│       ├── rules/                  ← skill rules (immutable)
│       │   └── rules.index.md
│       └── templates/
└── rules/                          ← project rules (mutable)
    ├── rules.index.md              ← root category map
    └── <category>/
        └── rules.index.md          ← per-category file map
```

## Skills

Skills are invoked either by:

- **Slash command** — `/skill-name` followed by a natural-language request
- **Keyword match** — an AI agent matches free-form requests against the **Keywords** column below

## Skill Catalog

### Registry

| Domain | Keywords | Skill | Actions |
|--------|----------|-------|---------|
| <Display> | <kw1, kw2, kw3> | `$<DOMAIN>_SKILL` | <action1, action2> |

## Rules

"**Rules**" is the umbrella term for the guardrails that govern how agents and skills behave (standards, policies, conventions, guidelines — one set of guardrails). Two tiers share the same folder name `rules/`; the path encodes the tier.

### Skill Rules (Immutable)

Skills bundle their own rules in `$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/`. These are immutable per project — they ship with the skill.

### Project Rules (Mutable)

Project rules live under `$AGENT_SYSTEM_FOLDER/rules/`. These are mutable and customised per repository.

### Mutability Contract

Mutability is signalled by path alone. There is no frontmatter, badge, or column denoting it.

### Precedence Model

- **Default:** when a project rule and a skill rule cover the same area, the project rule wins.
- **Exception:** a skill MAY mark specific rules as non-overridable in its own `rules.index.md`; those rules win regardless.

### Loading Rules

1. Start at `$AGENT_SYSTEM_FOLDER/rules/rules.index.md` (root category map)
2. Follow the matching `Load when` trigger into a per-category `rules.index.md` (file map)
3. Load only the leaf rule files matched by your task
-->
