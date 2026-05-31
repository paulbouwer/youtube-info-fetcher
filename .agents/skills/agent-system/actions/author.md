# Author Agent-System Asset

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Create a new agent-system asset, or modify an existing one, by routing the request to the matching rule and template based on the asset's type. Walks the user through populating the asset, then validates the result against the bundled validation checklist before writing.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Determine Asset Type and Mode

**Goal:** Identify what asset is being authored and whether the operation is a create or modify.

Inspect the user's request and any referenced file paths.

**Resolve mode:**

| Signal | Mode |
|--------|------|
| User references a path that does not yet exist | Create |
| User references a path that exists | Modify |
| User describes the asset without a path | Ask the user whether they want to create or modify, and where |

**Resolve asset type** using the routing table in `$AGENT_SYSTEM_SKILL/SKILL.md` § Asset-Type Routing. If the asset type is ambiguous (e.g. a file at `<domain>/checklist.md` could be a domain checklist), ask the user to confirm.

If the asset is a **per-domain rules family** (`tooling.md` + `development-environment.md` + `devcontainer.md` + `vscode.md`), confirm whether the user wants to author the full family or a single sibling.

### Step 2: Load Matching Rule and Template

**Goal:** Bring the relevant rule and template into focus.

The rule and template matching the asset type are in context (loaded by `SKILL.md`). For this step:

1. Re-read the rule matching the asset type.
2. Re-read the template matching the asset type.
3. Re-read `$AGENT_SYSTEM_SKILL/rules/conventions.md` (universal rules apply to every asset).

For per-domain `devcontainer.md` or `vscode.md` files, the matching rule is `$AGENT_SYSTEM_SKILL/rules/development-environment.md` (which documents the whole family); the matching template is the file-specific template (`devcontainer.md` or `vscode.md`).

### Step 3: Populate the Asset

**Goal:** Produce a draft asset that satisfies the rule.

**Create mode:** Start from the matching template. Replace placeholders (e.g. `<skill-name>`, `<Domain>`, `<tool-name>`) with concrete values gathered from the user. For multi-section templates (e.g. `agents-md.md` covers two shapes), keep only the section block matching the file being authored.

**Modify mode:** Read the existing file. Apply the user-requested changes. Preserve existing content that the change does not touch.

In both modes, work through the rule's required sections in order. Ask the user only the questions needed to fill in placeholders the rule requires:

- For a `SKILL.md`: skill name, description, capabilities with `Load when` triggers, the lean `Rules` index-pointer, bundled templates with `Load when` (if any), prerequisites, any operational concerns.
- For an action: action name, purpose, steps (with goals), error scenarios.
- For a rule: domain title, Quick Reference rules, domain sections.
- For a checklist: validation groups and items, each group's `§` reference into the rules authority.
- For an `AGENTS.md`: which shape (root or agent-system); the variables defined; for agent-system, the full Skill Catalog.
- For a `rules.index.md`: the categories (root variant) or leaf files (per-category variant), with `Load when` triggers.
- For a domain `core.md`: domain overview, principles, domain sections.
- For a domain `tooling.md`: tool catalog, per-tool blocks (Purpose, Runtime, Config file, configuration, rationale).
- For a domain `development-environment.md`: tool requirements list, host environments, workbenches.
- For a domain `devcontainer.md`: runtimes (omit if empty), tools with feature URIs and pins, lifecycle hooks.
- For a domain `vscode.md`: extensions satisfying tool requirements, optional authoring aids, settings (only material differences from defaults).

Never fabricate content. If the user has not provided enough detail to complete a required section, ask.

Apply universal rules from `conventions.md` while populating: every variable use is a `$VARIABLE_NAME`, no bare relative paths, no emojis, no `---` separators, no orchestrator names in rules, passive voice for prescriptive claims.

### Step 4: Self-Validate

**Goal:** Confirm the draft is conformant before presenting it.

Run the relevant sections of `$AGENT_SYSTEM_SKILL/rules/validation.md` against the draft:

1. The **Universal** group always.
2. The asset-type group matching the file (e.g. **Skill Manifest** for a `SKILL.md`).
3. The **Cross-Cutting** group always.

Fix any failures by revisiting the relevant rule. Do not present a draft with known validation failures.

### Step 5: Confirm and Write

**Goal:** Get user approval, then write the file.

Present the complete draft to the user for review. List any decisions made during population (e.g. "selected `Settings: none` for `<extension>` because no overrides were needed").

Do not write the file until the user explicitly approves.

On approval:

1. For Create mode, ensure the parent directory exists; write the file.
2. For Modify mode, write the updated content in place.
3. If the asset registers itself in another file (e.g. a new skill needs a Catalog row in `$AGENT_SYSTEM_FOLDER/AGENTS.md`; a new domain needs an entry in the root `rules.index.md`), prompt the user to author that follow-up edit. Do **not** silently modify other files.

Confirm creation/modification and summarise next steps:

- For a new skill: register it in `$AGENT_SYSTEM_FOLDER/AGENTS.md` (variable + Catalog row).
- For a new domain: register it in `$AGENT_SYSTEM_FOLDER/rules/rules.index.md` (and add a per-category `rules.index.md` if creating a new category folder).
- For any asset: run `review` against the new file (and any follow-up files) to confirm conformance.

## Error Handling

| Scenario | Action |
|----------|--------|
| Asset type ambiguous from path or request | Ask the user; do not guess |
| Required template placeholder cannot be filled (missing user info) | Ask the user; never fabricate |
| Existing file conflicts with Create mode target | Switch to Modify mode after confirming with the user |
| Validation surfaces a failure that the rule does not cover | Document the gap; surface to the user; do not silently bypass |
| User asks for content that violates a rule | Cite the rule and the file it is defined in; ask whether the user wants to revise the request or escalate to a rules change |
| User asks to introduce an `agents/` or `commands/` folder | Refuse; cite `$AGENT_SYSTEM_SKILL/rules/conventions.md § Skill Invocation` (no separate agent/command file types) |
