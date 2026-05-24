# Rules Index Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring a `rules.index.md` file. Every `rules/` folder — root and each category sub-folder, in both the project tier (`$AGENT_SYSTEM_FOLDER/rules/`) and the skill tier (`$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/`) — MUST contain a `rules.index.md`. Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`.

| Item | Rule |
|------|------|
| **Filename** | `rules.index.md` (the `rules.` prefix is preserved because the file exists in many folders across many trees) |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/rules-index.md` |
| **Two variants** | Root (category map) and per-category (file map) |
| **Required H2 sections** | Variables, How to Use This Index, Categories (root) or Rules (per-category), Non-Overridable Rules (skill tier, when present) |
| **Heading cap** | H3 |
| **Schema lean-ness** | No `Mutability` or `Overrides` columns — mutability is encoded by path (the tier) |

## Purpose

The two-level lazy-loading index for the Rules umbrella. The pattern keeps each index small and on-purpose so agents pull only the context they need.

- **Root `rules.index.md` — category map.** Documents the categories (nodes) under the rules tree, each with a `Load when` trigger. Sits at `$AGENT_SYSTEM_FOLDER/rules/rules.index.md` (project tier) and at `$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/rules.index.md` (skill tier).
- **Per-category `rules.index.md` — file map.** Documents the leaf rule files (rules) in that category, each with its purpose and a `Load when` trigger. Sits at `$AGENT_SYSTEM_FOLDER/rules/<category>/rules.index.md` (and deeper) and at the equivalent paths in the skill tier.

If a `rules/` folder has no sub-folders (flat rule files at the root), the root `rules.index.md` acts as the file map for those files. Categories are added only when the folder grows sub-folders.

## Two-Level Lazy-Loading Pattern

```text
$AGENT_SYSTEM_FOLDER/rules/                       ← project tier
├── rules.index.md                                 (category map)
├── languages/
│   ├── rules.index.md                             (file map for this category)
│   ├── go/
│   │   ├── rules.index.md                         (file map for this domain)
│   │   ├── core.md
│   │   └── …
│   └── bash/
│       └── …
└── formats/
    └── …

$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/        ← skill tier
├── rules.index.md                                 (file map; or category map if categories exist)
├── core.md
└── …
```

Agents load the root index first, identify the relevant category from `Load when`, follow the link to the per-category index, identify the relevant leaf file from its `Load when`, then load only that file. This avoids pulling unrelated context.

## Schema

### Root Index — Category Map

```markdown
## Categories

| Category | Covers | Load when |
|----------|--------|-----------|
| [`<category>`](<category>/rules.index.md) | <one-line description of what the category contains> | <concrete trigger phrase> |
```

- **Category** — link to the category's own `rules.index.md`, not to a leaf file
- **Covers** — what the category contains, in one line
- **Load when** — concrete trigger phrase (e.g. "writing or reviewing Go code", "configuring CI for a new language") — not vague ("when needed")

### Per-Category Index — File Map

```markdown
## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`<rule>.md`](<rule>.md) | <one-line description of what this rule covers> | <concrete trigger phrase> |
```

- **Rule** — link to the leaf rule file
- **Purpose** — one-line description; never restate the rule itself
- **Load when** — concrete trigger phrase

No `Mutability` column: the path encodes the tier (`<skill>/rules/` = skill rules, immutable; `$AGENT_SYSTEM_FOLDER/rules/` = project rules, mutable). No `Overrides` column: precedence defaults to "project overrides skill"; the only exception is documented in the **Non-Overridable Rules** section below (skill tier only).

## Non-Overridable Rules (Skill Tier Only)

A skill MAY mark specific rules as **non-overridable** — meaning a project rule covering the same area MUST NOT win, regardless of the default precedence. The mechanism is a top-level section in the skill's root `rules.index.md`:

```markdown
## Non-Overridable Rules

These rules win regardless of any project-rule counterpart. Project rules covering the same area MUST defer.

- [`<category>/<rule>.md`](<category>/<rule>.md) — <rationale: why this rule is invariant>
```

Use this sparingly and only for invariants that protect the skill's correctness. The default — project rules override skill rules — should be the rule, not the exception. The section is omitted in project-tier indexes and in skill-tier indexes that mark nothing as non-overridable.

## How to Use This Index — Required Prose

Every `rules.index.md` MUST open with a brief "How to Use This Index" section so a fresh agent knows the loading protocol without prior context.

```markdown
## How to Use This Index

This file is a lazy-loading index for <project rules | skill rules under `$<SKILL>_SKILL`>. To find the rules relevant to your task:

1. Identify the category (root index) or rule (per-category index) whose `Load when` matches your task
2. Follow the link
3. Load only the matched file — not the whole tree
```

## Rules

- The index never duplicates the content of the rule files it lists — only metadata (link, purpose, `Load when`)
- Every `rules/` folder (root and category sub-folder, both tiers) MUST contain a `rules.index.md`
- Every link in an index MUST resolve to an existing file
- The `Load when` column MUST be a concrete trigger phrase, not vague filler
- Root indexes are category maps; per-category indexes are file maps. A flat-only folder's root index acts as the file map until categories are added.
- The schema is `Category | Covers | Load when` (root) and `Rule | Purpose | Load when` (per-category). Adding `Mutability` or `Overrides` columns is non-conformant — mutability is encoded by path and precedence is documented in `$AGENT_SYSTEM_SKILL/rules/conventions.md § Precedence Model`.
- The **Non-Overridable Rules** section exists only in skill-tier root indexes that declare exceptions to the default precedence
