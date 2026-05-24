# Agent System

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- `AGENT_SYSTEM_SKILL := $AGENT_SYSTEM_FOLDER/skills/agent-system`
- `DEVCONTAINER_SKILL := $AGENT_SYSTEM_FOLDER/skills/devcontainer`
- `DECISION_RECORDS_SKILL := $AGENT_SYSTEM_FOLDER/skills/decision-records`
- `GIT_WORKFLOW_SKILL := $AGENT_SYSTEM_FOLDER/skills/git-workflow`
- `WORK_TRACKING_SKILL := $AGENT_SYSTEM_FOLDER/skills/work-tracking`

## Overview

This agent system provides a structured approach to AI-assisted development through skills, rules, and agent definitions. It is designed to be composable — load only what is relevant to the task at hand.

## Philosophy

- **Specialisation** — Each skill focuses on a specific domain, providing deep expertise
- **Isolation** — Sessions operate with focused context, reducing noise and improving accuracy
- **Composability** — Skills and rules can be mixed and matched per session as needed
- **Lazy Loading** — Load only the skills and rules relevant to the current task

## Structure

```bash
$AGENT_SYSTEM_FOLDER/
├── AGENTS.md                 # This file — system overview and skill catalog
├── skills/                   # Self-contained skill packages
│   └── <skill>/
│       ├── SKILL.md          # Skill manifest
│       ├── actions/          # Executable actions
│       ├── rules/            # Skill rules (immutable; ship with the skill)
│       │   └── rules.index.md # Per-skill lazy-loading index
│       └── templates/        # Document templates
└── rules/                    # Project rules (mutable; customised per repo)
    └── rules.index.md        # Root category map for project rules
```

## Skills

**Skills** are the core assets of the agent system. Each skill is a self-contained package that bundles actions (what to do) with rules (how to do it).

A skill is invoked either by:

- **Slash command** — `/skill-name` followed by a natural-language request that steers the skill toward a specific action (e.g. `/git-workflow help me create a branch`).
- **Keyword match** — an AI agent reading a free-form request matches it against the **Keywords** column of the registry below and loads the relevant skill.

Once loaded, the skill resolves the user's intent to one of its actions (see each skill's **Capabilities** table).

## Skill Catalog

### Registry

| Domain | Keywords | Skill | Actions |
|--------|----------|-------|---------|
| Agent System | agent-system, skill, action, rule, checklist, AGENTS.md, agentic asset | `$AGENT_SYSTEM_SKILL` | author, review |
| DevContainer | devcontainer, dev container, .devcontainer, development container | `$DEVCONTAINER_SKILL` | create, review |
| Decision Records | decision record, DR, architectural decision, ADR | `$DECISION_RECORDS_SKILL` | create, review |
| Git Workflow | git, branch, commit, pull request, PR, conventional commit | `$GIT_WORKFLOW_SKILL` | create-branch, commit, create-pull-request |
| Work Tracking | issue, work item, work package, board, tracking, sprint, acceptance criteria | `$WORK_TRACKING_SKILL` | create-issue, update-issue, create-work-package, update-work-package |

## Rules

**Rules** is the umbrella term for the guardrails that govern how agents and skills behave. The concept spans standards, policies, conventions, and guidelines — one set of guardrails ensuring agents and skills observe defined expectations. Sub-flavours such as "standard", "policy", or "convention" MAY appear inside the prose of individual rule files where they add clarity; "Rules" is the canonical word in all structural contexts (folder names, headings, registries, asset-type labels).

Rules live in two tiers, distinguished by path:

- **Skill rules** — bundled under `$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/`. They ship with the skill and define its invariant process.
- **Project rules** — under `$AGENT_SYSTEM_FOLDER/rules/`. They customise the system for this repository.

### Mutability Contract

The tier encoded in the path determines mutability:

- **Skill rules are immutable per project.** Treat them as read-only when working within a consuming project. They are owned by the skill author; revisions belong upstream in the skill itself.
- **Project rules are mutable.** Files under `$AGENT_SYSTEM_FOLDER/rules/` exist precisely so this repository can deviate from the skill defaults; edit them freely.

Mutability is signalled by path alone. There is no frontmatter or per-file flag.

### Precedence Model

When a skill rule and a project rule cover the same area:

1. **Project rules override skill rules by default.** This makes project rules the lever for repo-specific deviation.
2. **A skill MAY mark specific rules as non-overridable** in its own `rules.index.md` under a `## Non-Overridable Rules` section. Rules listed there win regardless of any project counterpart — they are the skill's guardrails of last resort. A project rule that covers the same area is ignored; surface the conflict and defer to the skill rule.

### Loading Rules — Two-Level Lazy Loading

Both tiers use the same lazy-loading shape: a registry file named `rules.index.md` lives at the root of each rules tree and in each category sub-folder. The root file is a **category map**; per-category files are **file maps**.

To load project rules for a task:

1. Read `$AGENT_SYSTEM_FOLDER/rules/rules.index.md` (the root category map) and find the category whose `Load when` trigger matches the task
2. Open that category's `rules.index.md` (file map) and find the leaf rule files whose `Load when` triggers match
3. Load only those leaf files — not the whole tree

To load skill rules for a task, follow the same pattern starting from `$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/rules.index.md`. Most skill `rules/` folders are flat, in which case the index is a file map directly.

When both tiers apply, load skill rules first, then project rules, then apply the Precedence Model above to resolve overlaps.
