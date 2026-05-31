# Research — SKILL.md Lazy-Loading Gap Analysis

## Source of Learnings

The refinements analysed here were observed in a separate reference repository that
carried the agent-system's progressive/lazy-loading idea further than this repo had.
This document records the *pattern* only — abstracted from that repository — so it can
be evaluated and applied here. No external repository names or paths are reproduced.

The comparison was scoped (per the work package goal) to two areas:

1. `Load when` triggers attached to SKILL.md routing tables.
2. Refinements to the SKILL.md manifests around how rules are loaded.

Other context-efficiency optimisations from the reference were assessed and found to be
**already present** in this repo (notably the two-level `rules.index.md` lazy index,
which already carries a `Load when` column at both the root category map and per-category
file map levels).

## The Stated Philosophy

This repo's `.agents/AGENTS.md` already states the intent:

> **Lazy Loading** — Load only the skills and rules relevant to the current task.

The gap is therefore an **implementation error**, not a change of direction: the SKILL.md
layer does not honour the lazy-loading philosophy the system already declares.

## Current State (eager) vs Corrected State (lazy)

### SKILL.md routing manifest

| Aspect | Current (eager) | Corrected (lazy) |
|--------|-----------------|------------------|
| Capabilities table | `Capability \| Action \| Description` | adds a **`Load when`** column — an intent trigger per capability |
| Rules section | `### Bundled Rules` enumerates **every** rule file with a description | replaced by a lean **`### Rules`** index-pointer table (Skill Rules Index, and Project Rules Index where a 1:1 project-rules domain exists) plus the instruction: *"Consult each index and load only the rule files whose `Load when` matches the selected capability"* |
| Bundled Templates table | `Template \| File \| Description` | adds a **`Load when`** column (templates stay enumerated; they are few and capability-selected) |
| Flow / Execution Steps | Step 1: *"Load the bundled rules … into context"* — eager-loads **all** bundled rules up front, before routing | route first via `Load when`, then load **only** the capability-matched rules, then the capability-matched templates, then execute |

Corrected Flow (Execution Steps):

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger.
2. Load skill and project rules via the indexes listed in the **Rules** section — only the rules whose **Load when** matches the selected capability.
3. Load bundled templates whose **Load when** matches the selected capability. *(skills with templates only)*
4. Execute the selected capability.
5. Domain-specific post-action step, if any (e.g. index maintenance).

### Action manifests

| Aspect | Current (eager residual) | Corrected (lazy) |
|--------|--------------------------|------------------|
| `### Prerequisites` precondition | *"assumes **all bundled** rules [and templates] have been loaded into context by `SKILL.md`"* | *"assumes the rules [and templates] **relevant to this capability** have been loaded into context by `SKILL.md`"* |
| Self-loading | Action does not self-load rules (correct — keep) | Unchanged: the skill performs the lazy selection; the action never self-loads bundled rules |

The `and templates` suffix is retained only for skills that bundle skill-level templates.

## Affected-File Inventory (this repo)

### Authority — agent-system skill (codifies the doctrine)

- `rules/skill.md` — Purpose, Configuration-tables spec, Flow Pattern, Rules list.
- `templates/skill.md` — Configuration tables + Execution Steps.
- `rules/action.md` — action precondition wording.
- `templates/action.md` — action precondition wording.
- `rules/validation.md` — drop the "Bundled Rules table required" and "load bundled rules before templates before routing" checks; add checks for the `Load when` column on Capabilities (and Bundled Templates), the lean `Rules` index-pointer, and capability-scoped action preconditions.
- `rules/checklist.md` — **no change** (encodes no loading doctrine).

### SKILL.md reflow (5)

- `agent-system`, `decision-records`, `devcontainer`, `git-workflow`, `work-tracking`.
- `grill-me` is **exempt** — it has no Configuration/Capabilities (interactive skill).

### Action precondition reword (15 + 2 authority restatements)

- agent-system: `author.md`, `review.md` *(rules and templates)*
- decision-records: `create.md`, `review.md` *(rules and templates)*
- devcontainer: `create.md`, `review.md` *(rules and templates)*
- git-workflow: `commit.md`, `create-branch.md`, `create-pull-request.md` *(rules)*
- work-tracking: `create-issue.md`, `create-work-package.md`, `update-issue.md`, `update-work-package.md` *(rules)*
- authority restatements: `agent-system/rules/action.md`, `agent-system/templates/action.md`

### Bundled Templates `Load when` column (3)

- `agent-system`, `decision-records`, `devcontainer` (the skills with skill-level templates). `git-workflow` and `work-tracking` bundle no skill templates.

## Per-Skill Project-Rules Pointer Nuance

The reference's lean `Rules` table carried a *Project Rules Index* row for skills that map
1:1 to a project-rules domain. In this repo:

- `git-workflow` → Project Rules Index = `$AGENT_SYSTEM_FOLDER/rules/git-workflow/rules.index.md`
- `work-tracking` → Project Rules Index = `$AGENT_SYSTEM_FOLDER/rules/work-tracking/rules.index.md`
- `agent-system`, `decision-records`, `devcontainer` → no single project-rules domain; their actions load project rules **by detected context** (languages/formats/provider). For these, the `Rules` table lists the Skill Rules Index only, and the existing "actions load additional project rules based on detected project context" sentence is retained.

## What is Explicitly Out of Scope

- The `rules.index.md` lazy index layer (already correct in this repo).
- `AGENTS.md` skill catalog (already uses a `Keywords` column; matches the reference).
- `grill-me` (interactive, no routing manifest).
- Any non-loading optimisation already present in this repo.
