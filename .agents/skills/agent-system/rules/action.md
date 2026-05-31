# Action Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL`, `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring an action file at `$AGENT_SYSTEM_FOLDER/skills/<domain>/actions/<action>.md`. Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`.

| Item | Rule |
|------|------|
| **Path** | `$AGENT_SYSTEM_FOLDER/skills/<domain>/actions/<action>.md` |
| **Filename** | `kebab-case`, verb or verb-phrase (`create.md`, `review.md`, `create-branch.md`) |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/action.md` |
| **Reference implementations** | `$DECISION_RECORDS_SKILL/actions/create.md`, `$DECISION_RECORDS_SKILL/actions/review.md` |
| **Required H2 sections** | Variables, Purpose, Flow |
| **Heading cap** | H3 (with H4 only inside Step content when essential) |

## Purpose

An action is a step-by-step workflow for a specific capability. It assumes the skill manifest has already loaded the rules relevant to this capability — and any templates, where the skill bundles them. Actions are routed to from `SKILL.md`'s Capabilities table.

## File Structure

| Section | Required | Purpose |
|---------|----------|---------|
| `## Variables` | Yes | Inheritance declaration |
| `## Purpose` | Yes | One-sentence description of what this action does |
| `## Flow` | Yes | Prerequisites + numbered steps |
| `## Error Handling` | If needed | Table of scenarios and recovery actions |

## Flow Pattern

```markdown
### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

### Step 1: {Verb Phrase}

**Goal:** One sentence describing the step's outcome.

[Step content]

### Step 2: {Verb Phrase}

**Goal:** …
```

## Rules

- No bundled skill-rules loading steps — `SKILL.md` owns capability-scoped skill-rule loading. An action that loads its own bundled skill rules is non-conformant. (Actions may still load *project* rules via the explicit project-context detection pattern below.)
- No operational concerns (e.g. index maintenance, post-action file moves) — those are aggregated in `SKILL.md`. The action references them by section name when execution requires them.
- Each step starts with a **Goal** sentence. Steps without a goal read as instructions stripped of intent.
- Steps use descriptive verb-phrase titles (e.g. "Assess Context", "Guided Discovery", "Construct Y-Statement"). Avoid vague titles like "Process" or "Handle".
- Error-handling content uses a table format: `| Scenario | Action |`. Inline error prose scattered through steps is harder to scan.
- Cross-references to rules and templates use full variable paths: `$<SKILL>_SKILL/rules/core.md`, `$<SKILL>_SKILL/templates/<template>.md`.
- Validation references point at the validation/checklist file: `$<SKILL>_SKILL/rules/checklist.md` (or `validation.md` where the skill uses that name).

## Project-Rules Loading

When an action needs project rules (languages, formats, tools, git-workflow, work-tracking) in addition to the skill's bundled rules, the action — not `SKILL.md` — decides which project rules to load, based on the detected project context. Document the detection step explicitly:

> **Detect project context.** Inspect the repository to determine which project rules apply (e.g. read `$AGENT_SYSTEM_FOLDER/AGENTS.md` for `$REPO_PROVIDER`; check `go.mod` / `package.json` / `pyproject.toml` for the language). Load the matching rules from `$AGENT_SYSTEM_FOLDER/rules/`.

Honour the precedence model from `$AGENT_SYSTEM_SKILL/rules/conventions.md § Precedence Model` when both a project rule and a skill rule cover the same area: project rules win by default; only a skill-declared non-overridable rule wins regardless.
