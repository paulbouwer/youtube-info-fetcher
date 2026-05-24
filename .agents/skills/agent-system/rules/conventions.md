# Agent-System Conventions Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL`, `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Universal rules that apply to every agent-system asset, regardless of asset type. Per-asset shape rules live in the sibling rules (`skill.md`, `action.md`, `rule.md`, `checklist.md`, `agents-md.md`, `rules-index.md`, `domain-core.md`, `tooling.md`, `development-environment.md`).

| Item | Rule |
|------|------|
| **Reference implementation** | Decision Records skill (`$DECISION_RECORDS_SKILL`) |
| **Variable definitions** | Root `AGENTS.md` and `$AGENT_SYSTEM_FOLDER/AGENTS.md` only |
| **Variable format** | `VARIABLE_NAME := value` (no trailing slash) |
| **Variable usage** | Always `$VARIABLE_NAME` — never bare paths for known variables |
| **Separators** | No `---` horizontal rules — headings provide structure |
| **Emojis** | None — use `Good:`/`Bad:` labels, plain text severity |
| **One-way visibility** | Rules never name orchestrators or build mechanisms |
| **Rules vocabulary** | "Rules" is the umbrella; sub-flavours (standard, policy, convention) MAY appear inside individual rule files where they add clarity |
| **Tier signalled by path** | `<skill>/rules/` = skill rules (immutable); `$AGENT_SYSTEM_FOLDER/rules/` = project rules (mutable) |
| **Precedence** | Project rules override skill rules by default; a skill MAY mark specific rules non-overridable in its `rules.index.md` |

## Variable System

### Hierarchy

Variables flow downward through a strict inheritance chain. No file defines a variable that belongs to a higher level.

```
Root AGENTS.md (defines $AGENT_SYSTEM_FOLDER, $PROJECT_FOLDER, $REPO_PROVIDER)
  └── $AGENT_SYSTEM_FOLDER/AGENTS.md (inherits above, defines skill variables)
        ├── skills/<skill>/SKILL.md (inherits all, defines none)
        │     ├── actions/*.md (inherits all, defines none)
        │     ├── rules/*.md (inherits all, defines none)
        │     └── templates/*.md (inherits all, defines none)
        └── rules/rules.index.md (inherits all, defines none)
```

### Definitions

Variables are defined in exactly two places:

| File | Defines | Format |
|------|---------|--------|
| Root `AGENTS.md` | `$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, `$REPO_PROVIDER`, `$WORK_TRACKING_PROVIDER`, `$WORK_TRACKING_PROJECT` | `VARIABLE_NAME := value` |
| `$AGENT_SYSTEM_FOLDER/AGENTS.md` | Skill variables (`$AGENT_SYSTEM_SKILL`, `$DEVCONTAINER_SKILL`, etc.) | `SKILL_NAME := $AGENT_SYSTEM_FOLDER/skills/<domain>` |

### Naming

- Skill variables are namespaced to prevent clashes when multiple skills are loaded: `$DECISION_RECORDS_SKILL` not `$SKILL_FOLDER`
- Use `UPPER_SNAKE_CASE`
- No trailing slash on any variable value

### Variables Block

Every agent-system markdown file that uses variables MUST include a `## Variables` section immediately after the title. The block declares what the file inherits — it never defines new variables.

**Pattern:**

```markdown
## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$<SKILL_NAME>_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`
```

Include only the variables the file actually uses. If a rule only uses `$PROJECT_FOLDER`, it still declares the full inheritance line from root `AGENTS.md`.

### Usage Rules

- Always use `$VARIABLE_NAME` when referencing a path covered by a defined variable
- Never use bare relative paths like `skills/decision-records/` when `$DECISION_RECORDS_SKILL` exists
- In-file cross-references (e.g. an action referencing its own skill's rule) still use the skill variable: `$DECISION_RECORDS_SKILL/rules/core.md`

## Rules Vocabulary

"**Rules**" is the umbrella term for the guardrails that govern how agents and skills behave. The concept spans standards, policies, conventions, and guidelines — a single set of guardrails ensuring agents and skills observe defined expectations.

- The canonical word in every structural context (folder names, headings, registries, AGENTS.md sections, philosophy) is **Rules**.
- Sub-flavours (standard, policy, convention, guideline) MAY appear inside the prose of individual rule files where they add clarity. They MUST NOT be promoted into structural contexts (folder names, registry headings, asset-type labels).
- Two tiers exist:
  - **Skill rules** — bundled under `$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/`. Immutable per project; ship with the skill.
  - **Project rules** — under `$AGENT_SYSTEM_FOLDER/rules/`. Mutable; customised per repository for how this repo expects to operate.
- Use the qualifiers "skill rules" and "project rules" in prose to reinforce the tier and its mutability. The path already encodes the tier; the qualifier reinforces it.

## Mutability Contract

- **Skill rules are immutable per project.** Files under any `$AGENT_SYSTEM_FOLDER/skills/<skill>/rules/` ship with the skill and MUST NOT be edited as part of project work. They evolve only through changes to the skill itself.
- **Project rules are mutable per project.** Files under `$AGENT_SYSTEM_FOLDER/rules/` are the customisation surface. Projects MAY add, modify, or remove project rules to fit their operating model.
- Mutability is signalled by path alone — there is no frontmatter, badge, or column denoting it. The two tiers use the same folder name (`rules/`); the path encodes the tier.

## Precedence Model

When a project rule and a skill rule cover the same area:

- **Default:** the project rule wins. Project rules exist precisely to deviate from defaults; deferring to them is the expected behaviour.
- **Exception:** a skill MAY mark specific rules as **non-overridable** in its own `rules.index.md`. Rules listed as non-overridable win regardless of any project-rule counterpart. Skills use this lever sparingly and only for invariants that protect the skill's correctness.
- Category mirroring between skill rules and project rules is recommended but not enforced. Project rules MAY exist in categories that have no skill counterpart (e.g. `rules/languages/go/`).

Loaders (actions, agents) MUST resolve precedence at load time: if a rule file exists at the same conceptual area in both tiers, load the project rule unless the skill's `rules.index.md` flags that rule as non-overridable.

## Skill Invocation

Skills are the only first-class user-initiated asset in the agent system. They are invoked in one of two ways:

- **Slash command** — `/skill-name` followed by a natural-language request that steers the skill toward a specific action (e.g. `/git-workflow create a branch for issue 42`).
- **Keyword match** — an AI agent matches a free-form request against the **Keywords** column of the Skill Catalog registry in `$AGENT_SYSTEM_FOLDER/AGENTS.md` and loads the relevant skill.

There are no separate "agent" or "command" file types. Do not introduce `agents/` or `commands/` folders — any new capability belongs inside a skill, expressed as an action.

## One-Way Visibility

Rule files MUST NOT name orchestrators (e.g. "the devcontainer skill", "the assembly stage") or specific build mechanisms. Visibility is one-way: orchestrators read rules, never the reverse. Rules declare requirements in passive voice ("the host MUST provide", "the workbench MUST install"). This keeps rules stable as orchestrators change and prevents rules from becoming hostage to specific tooling vocabulary.

The same one-way principle bars per-setting rationale prose from naming sibling-doc structure. Phrase rationales as general-purpose justifications — not "useful for the two-layer dev-env doc's repeated subsection names" but "useful for documents that organise repeated blocks under multiple top-level groupings".

## Formatting Rules

### Do

- Use markdown headings (`##`, `###`) for structure
- Use tables for structured data (capabilities, rules, configuration)
- Use bullet points for role definitions and lists
- Use `Good:`/`Bad:` labels for examples
- Use code blocks for templates, formats, and file patterns
- Use `§` notation for cross-referencing specific sections within a rule file

### Do Not

- Use `---` horizontal rules as section separators
- Use emojis (no ✓, ✗, 🛑, ⚠, 📁, or similar)
- Use numbered scoring formulas with decimal weights
- Prescribe exact output format — describe the structure and let the agent adapt
- Duplicate content across files — reference the authoritative source

## Cross-Reference Rules

- Actions reference rules: `$<SKILL>_SKILL/rules/core.md`
- Validation files reference rules-authority sections: `$<SKILL>_SKILL/rules/core.md § Section Name`
- Actions reference templates: `$<SKILL>_SKILL/templates/<template>.md`
- Root `AGENTS.md` references the agent-system entry point: `$AGENT_SYSTEM_FOLDER/AGENTS.md`
- Avoid circular references — the hierarchy is: Skill → Action → Rule

## Composability

The system follows four design principles, restated here so authors of new assets internalise them:

- **Specialisation** — each skill focuses on a specific domain, providing deep expertise
- **Isolation** — sessions operate with focused context, reducing noise and improving accuracy
- **Composability** — skills and rules can be mixed and matched per session as needed
- **Lazy Loading** — load only the skills and rules relevant to the current task

A new asset that violates any of these principles (e.g. a skill that pulls in unrelated domains, an action that loads every rule upfront) is non-conformant.
