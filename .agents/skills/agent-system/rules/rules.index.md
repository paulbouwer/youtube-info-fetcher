# Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the skill rules bundled under `$AGENT_SYSTEM_SKILL`. To find the rules relevant to your task:

1. Identify the rule whose `Load when` matches your task
2. Follow the link
3. Load only the matched file — not the whole tree

The agent-system skill's rules folder is flat (no category sub-folders); this index is therefore a file map. A category map would replace it if categories were ever added.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`conventions.md`](conventions.md) | Universal rules across every agent-system asset — variable system, formatting, cross-references, one-way visibility, composability, Rules vocabulary, mutability contract, precedence model | Authoring or reviewing any agent-system asset (always loaded) |
| [`skill.md`](skill.md) | `SKILL.md` manifest authoring pattern | Authoring or reviewing a `SKILL.md` file |
| [`action.md`](action.md) | Action-file authoring pattern (`actions/<action>.md`) | Authoring or reviewing an action file |
| [`rule.md`](rule.md) | Skill-bundled rule-file authoring pattern (`rules/<topic>.md`) | Authoring or reviewing a skill-bundled rule (e.g. a skill's `core.md`) |
| [`checklist.md`](checklist.md) | Skill-bundled validation checklist asset pattern | Authoring or reviewing a `checklist.md` or `validation.md` inside a skill's `rules/` |
| [`agents-md.md`](agents-md.md) | Root and agent-system `AGENTS.md` authoring pattern | Authoring or reviewing either `AGENTS.md` file |
| [`rules-index.md`](rules-index.md) | `rules.index.md` registry pattern (root category map and per-category file map) | Authoring or reviewing any `rules.index.md` file (root or per-category, project tier or skill tier) |
| [`domain-core.md`](domain-core.md) | Per-domain `core.md` authoring pattern (`rules/<category>/<domain>/core.md`) | Authoring or reviewing a domain `core.md` under the project rules tree |
| [`tooling.md`](tooling.md) | Per-domain `tooling.md` authoring pattern | Authoring or reviewing a domain `tooling.md` |
| [`development-environment.md`](development-environment.md) | Per-domain dev-environment family pattern (index + `devcontainer.md` + `vscode.md`) | Authoring or reviewing a domain `development-environment.md`, `devcontainer.md`, or `vscode.md` |
| [`validation.md`](validation.md) | The agent-system skill's own validation checklist, used by the Review action | Running the Review action over any agent-system asset |

## Non-Overridable Rules

These rules win regardless of any project-rule counterpart. Project rules covering the same area MUST defer.

- [`conventions.md`](conventions.md) — Defines the universal contract (variable system, Rules vocabulary, mutability contract, precedence model itself). A project rule that overrode this would undermine the basis on which precedence is resolved.
- [`rules-index.md`](rules-index.md) — Defines the lazy-loading index schema and the precedence-marking mechanism. A project rule that overrode this would break the loading protocol agents use to find rules.
