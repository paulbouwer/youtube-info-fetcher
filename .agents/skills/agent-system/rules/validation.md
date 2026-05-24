# Agent-System Validation Checklist

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Single-pass validation for agent-system assets. Rules are defined in the per-asset rule files in `$AGENT_SYSTEM_SKILL/rules/`; this checklist references them via `§` notation and provides verifiable items grouped by asset type.

The `review` action runs this checklist against one or more assets, identifying the asset type per file and applying the matching group(s) plus the universal group.

## Universal (applies to every asset)

Validate against `$AGENT_SYSTEM_SKILL/rules/conventions.md`:

- [ ] **Variables block** — `## Variables` section present immediately after the title; declares only inherited variables; no new variables defined
- [ ] **Variable usage** — every reference to a path covered by a defined variable uses `$VARIABLE_NAME`, never a bare relative path
- [ ] **No undeclared variables** — every `$VARIABLE_NAME` used is defined in either root `AGENTS.md` or `$AGENT_SYSTEM_FOLDER/AGENTS.md`
- [ ] **No `---` separators** — only YAML frontmatter delimiters in skill files
- [ ] **No emojis** — no ✓, ✗, 🛑, ⚠, 📁, or similar; use `Good:` / `Bad:` labels
- [ ] **No bare orchestrator names** — rules do not name "the devcontainer skill", "the assembly stage", or specific build mechanisms (one-way visibility)
- [ ] **Cross-references** — use full variable paths (`$<SKILL>_SKILL/rules/core.md`) and `§` notation for section references
- [ ] **Rules vocabulary** — structural contexts (folder names, headings, registries, asset-type labels) use "Rules"; sub-flavours (standard, policy, convention) only appear in prose where they add clarity
- [ ] **No legacy `standards/` paths** — no references to `standards/` paths or to "standards" as the umbrella concept remain
- [ ] **Tier qualifier in prose** — references to either tier use "skill rules" or "project rules" rather than ambiguous "rules"

## Skill Manifest (`SKILL.md`)

Validate against `$AGENT_SYSTEM_SKILL/rules/skill.md § File Structure`:

- [ ] Frontmatter present with `name` (kebab-case, matches folder) and `description` (action-phrased)
- [ ] `## Variables`, `## Purpose`, `## Configuration`, `## Flow` sections present
- [ ] **Capabilities** table present with `Capability | Action | Description` columns
- [ ] **Bundled Rules** table present with `Rule | File | Description` columns
- [ ] **Bundled Templates** table present with `Template | File | Description` columns
- [ ] All paths in tables use skill variables, never bare paths
- [ ] **Flow** section has `### Prerequisites` checklist and `### Execution Steps` numbered list
- [ ] Execution Steps load bundled rules before bundled templates before routing intent
- [ ] No domain-specific operational concerns scattered into actions — aggregated in `SKILL.md`
- [ ] Heading depth ≤ H3

## Action (`actions/<action>.md`)

Validate against `$AGENT_SYSTEM_SKILL/rules/action.md § File Structure`:

- [ ] Filename is kebab-case verb or verb-phrase
- [ ] `## Variables`, `## Purpose`, `## Flow` sections present
- [ ] `### Prerequisites` declares rules/templates already loaded by `SKILL.md`
- [ ] No bundled-rules loading inside the action (SKILL.md owns this)
- [ ] No operational concerns inside the action (SKILL.md owns this)
- [ ] Each step starts with a `**Goal:**` sentence
- [ ] Step titles use descriptive verb-phrases
- [ ] `## Error Handling`, if present, uses `| Scenario | Action |` table format
- [ ] Project-rules loading (if any) explicitly declares the detection step and honours the precedence model

## Rule (`rules/<topic>.md`)

Validate against `$AGENT_SYSTEM_SKILL/rules/rule.md § File Structure`:

- [ ] `## Variables`, `## Quick Reference`, then domain sections
- [ ] Quick Reference is a table at the top providing at-a-glance rules
- [ ] `Good:` / `Bad:` labels used for examples (no emojis)
- [ ] Templates referenced via `$<SKILL>_SKILL/templates/<template>.md`
- [ ] Rules are principle-based, not category-based, where possible
- [ ] No restating of universal rules from `conventions.md` — references upstream

## Checklist (`rules/checklist.md` or `validation.md`)

Validate against `$AGENT_SYSTEM_SKILL/rules/checklist.md § File Structure`:

- [ ] `## Variables`, `## Overview`, then validation groups
- [ ] Overview points at the rules authority
- [ ] Each validation group references the matching `§ <Section>` of the rules authority
- [ ] Items use `- [ ]` markdown task-list format
- [ ] No restated rules — references only
- [ ] Group order matches the rules authority's section order (where applicable)
- [ ] Every item is verifiable by inspection or deterministic check

## AGENTS.md (root + agent-system)

Validate against `$AGENT_SYSTEM_SKILL/rules/agents-md.md`:

### Root `AGENTS.md`

- [ ] At repository root
- [ ] Defines `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, plus repo-wide variables (`$REPO_PROVIDER`, etc.)
- [ ] Does NOT define skill variables
- [ ] Points to `$AGENT_SYSTEM_FOLDER/AGENTS.md` as the agent-system entry
- [ ] Does NOT enumerate skill internals
- [ ] Uses "Rules" umbrella vocabulary in prose where applicable

### Agent-system `AGENTS.md`

- [ ] At `$AGENT_SYSTEM_FOLDER/AGENTS.md`
- [ ] Inherits root variables; defines all skill variables
- [ ] `## Skill Catalog` present with `Domain | Keywords | Skill | Actions` table
- [ ] Every Catalog row's Skill column references a defined skill variable
- [ ] Every defined skill variable has a corresponding Catalog row (no orphans)
- [ ] Skills referenced by variable, not by bare path
- [ ] `## Rules` section documents the Rules umbrella, the two-tier model (skill rules / project rules), the mutability contract, and the precedence model
- [ ] Structure diagram shows `rules/` in both tiers and the `rules.index.md` registry files

## Rules Index (`rules/rules.index.md`, root or per-category)

Validate against `$AGENT_SYSTEM_SKILL/rules/rules-index.md`:

- [ ] `## Variables`, `## How to Use This Index`, then `## Categories` (root variant) or `## Rules` (per-category variant)
- [ ] Root indexes use schema `Category | Covers | Load when` and link to per-category `rules.index.md` files
- [ ] Per-category indexes use schema `Rule | Purpose | Load when` and link to leaf rule files
- [ ] No `Mutability` or `Overrides` columns — mutability is encoded by path
- [ ] Every link resolves to an existing file
- [ ] Every `Load when` cell is a concrete trigger phrase, not vague filler
- [ ] Skill-tier root index includes a `## Non-Overridable Rules` section iff the skill declares any; project-tier indexes do not

## Domain Core (`rules/<category>/<domain>/core.md`)

Validate against `$AGENT_SYSTEM_SKILL/rules/domain-core.md § File Structure`:

- [ ] `## Variables`, `## Overview` (one paragraph), `## Principles` (numbered list), then domain sections
- [ ] Principles use `**Word** — explanation` format
- [ ] No content duplicating sibling files (`tooling.md`, `development-environment.md`, etc.)
- [ ] MUST / SHOULD / MUST NOT in **bold** for requirements
- [ ] Code examples use fenced blocks with the language identifier
- [ ] Self-contained enough to support primary domain authoring without siblings

## Domain Tooling (`rules/<category>/<domain>/tooling.md`)

Validate against `$AGENT_SYSTEM_SKILL/rules/tooling.md`:

- [ ] `## Variables`, `## Overview` (two prescriptive bullets only), `## Tool Catalog`, then per-tool H2 sections
- [ ] Overview's two bullets state prescriptive framing and the single-config rule — nothing else
- [ ] Tool Catalog has `Type | Tool | Purpose` columns only (no Runtime / Config / Section-link columns)
- [ ] Each tool's H2 heading is the backticked tool name (`` ## `shellcheck` ``)
- [ ] No kind-grouping wrappers (no `## Linters`, `## Formatters`)
- [ ] Each per-tool block has: vertical `Field | Value` table → configuration section → closing cross-environment paragraph
- [ ] Runtime field uses minimum constraint syntax (`Node ≥ 20`) or `none`
- [ ] `Config file` qualified with section when multi-tenant (e.g. `` `.editorconfig` (`[*.sh]` block) ``)
- [ ] No installation mechanics, invocation tutorials, editor-extension names, or host-specific notes
- [ ] Formatter/linter alignment captured (per-rule cells or final `## <X> Alignment` paragraph)
- [ ] Rationale prose stays domain-agnostic (no naming of sibling-doc structure)

## Domain Development-Environment Family

Validate against `$AGENT_SYSTEM_SKILL/rules/development-environment.md`:

### Index (`development-environment.md`)

- [ ] `## Variables`, `## Overview` (two-layer model + congruence note), `## Tool Requirements`, `## Host Environments`, `## Workbenches`
- [ ] Tool Requirements is bullets only, each linking to a `tooling.md` anchor
- [ ] No tool-by-environment matrix
- [ ] Host and Workbench sections list per-environment files as links

### `devcontainer.md`

- [ ] `## Variables`, `## Overview`, `## Runtimes` (omitted when empty), `## Tools`
- [ ] Runtimes block omitted when every tool has `Runtime: none`
- [ ] Tools section flat (no Linters/Formatters sub-grouping)
- [ ] Each block is H3 with backticked name; vertical key/value table; jsonc snippet (when needed); optional Notes
- [ ] Language runtimes pinned to ≥ major.minor
- [ ] Tool pins documented or `{}` / `os-provided` choice acknowledged

### `vscode.md`

- [ ] `## Variables`, `## Overview`, `## Extensions Satisfying Tool Requirements`, optional `## Authoring Aids`
- [ ] MUST/SHOULD distinction between the two sections is explicit in the Overview
- [ ] Each Extension block has `Extension ID`, `Settings`, `Reads config from` rows
- [ ] Settings snippets contain only material differences from extension defaults
- [ ] Behaviour-changing setting removals flagged in the plan

## Cross-Cutting

- [ ] No `agents/` or `commands/` folders introduced — new capabilities belong inside a skill as actions
- [ ] No duplicated content — references point to authoritative source
- [ ] Trailing-slash consistency — no trailing slashes on variable definitions
- [ ] Templates and rules agree: every required section in a rule appears in the matching template; every section in a template traces back to a rule
