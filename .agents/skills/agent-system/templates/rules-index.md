<!--
Template covers BOTH `rules.index.md` variants.
Pick the variant matching the folder you are authoring; delete the other.
-->

# Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$<SKILL_NAME>_SKILL` (one line per skill that bundles rules) from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for <project rules | skill rules under `$<SKILL_NAME>_SKILL`>. To find the rules relevant to your task:

1. Identify the category (root index) or rule (per-category index) whose `Load when` matches your task
2. Follow the link
3. Load only the matched file — not the whole tree

<!-- =====================================================================
ROOT VARIANT (category map) — use when this index sits at the root of a rules tree (`rules/rules.index.md`) and the tree has category sub-folders.
===================================================================== -->

## Categories

| Category | Covers | Load when |
|----------|--------|-----------|
| [`<category>`](<category>/rules.index.md) | <one-line description of what the category contains> | <concrete trigger phrase> |
| [`<category>`](<category>/rules.index.md) | <one-line description> | <concrete trigger phrase> |

<!-- =====================================================================
PER-CATEGORY VARIANT (file map) — use when this index sits in a category sub-folder (e.g. `rules/<category>/rules.index.md`) and lists the leaf rule files in that folder. A root index for a flat-only folder (no sub-categories) also uses this variant until categories are added.
===================================================================== -->

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`<rule>.md`](<rule>.md) | <one-line description of what this rule covers> | <concrete trigger phrase> |
| [`<rule>.md`](<rule>.md) | <one-line description> | <concrete trigger phrase> |

<!-- =====================================================================
NON-OVERRIDABLE RULES (skill tier root index only) — include only when this index is the root of a skill's rules tree AND the skill declares one or more rules as non-overridable. Omit otherwise.
===================================================================== -->

<!--
## Non-Overridable Rules

These rules win regardless of any project-rule counterpart. Project rules covering the same area MUST defer.

- [`<category>/<rule>.md`](<category>/<rule>.md) — <rationale: why this rule is invariant>
-->
