# Rule Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL`, `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring a skill-bundled rule file (typically `$AGENT_SYSTEM_FOLDER/skills/<domain>/rules/core.md`, but any file in `rules/` named after a topic). Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`. For per-domain `core.md` files (under `$AGENT_SYSTEM_FOLDER/rules/<category>/<domain>/`), see `$AGENT_SYSTEM_SKILL/rules/domain-core.md`.

| Item | Rule |
|------|------|
| **Path** | `$AGENT_SYSTEM_FOLDER/skills/<domain>/rules/<topic>.md` |
| **Canonical filename** | `core.md` for the skill's primary rules authority |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/rule.md` |
| **Reference implementation** | `$DECISION_RECORDS_SKILL/rules/core.md` |
| **Required H2 sections** | Variables, Quick Reference, then domain sections |
| **Heading cap** | H3 |
| **Validation companion** | `$<SKILL>_SKILL/rules/checklist.md` (or `validation.md`) |
| **Tier** | Skill rules (immutable per project — see `$AGENT_SYSTEM_SKILL/rules/conventions.md § Mutability Contract`) |

## Purpose

A skill-bundled rule file is the authoritative reference for the skill's domain rules. It defines what is correct — actions and validation files reference it. A skill MAY have multiple rule files (e.g. `core.md` + topic-specific files); the canonical primary is `core.md`.

Sub-flavours such as "standard", "policy", or "convention" MAY appear in the prose of an individual rule file where they add clarity (e.g. "this section documents the encoding convention for …"). The structural label is always "rule".

## File Structure

| Section | Required | Purpose |
|---------|----------|---------|
| `## Variables` | Yes | Inheritance declaration |
| `## Quick Reference` | Yes | Summary table of key rules — at-a-glance contract |
| Domain sections | Yes | Detailed rules, formats, examples |

## Rules

- Quick Reference table at the top provides at-a-glance rules. AI agents that load the rule partway through a long context window benefit from the contract sitting at the top.
- Use `Good:`/`Bad:` labels for examples (no emojis like ✓/✗).
- Link to templates where applicable: `$<SKILL>_SKILL/templates/<template>.md`.
- Rules SHOULD be principle-based ("when the decision is costly to reverse") rather than category-based ("architecture decisions, technology decisions"). Categories date faster than principles.
- Domain sections use H2; sub-rules use H3. Cap at H3.
- The rule file does not restate rules from upstream universals — it references `$AGENT_SYSTEM_SKILL/rules/conventions.md` for cross-cutting matters (variables, formatting, etc.).
- One-way visibility applies (see `$AGENT_SYSTEM_SKILL/rules/conventions.md § One-Way Visibility`): no orchestrator names, no naming of consumer assemblies.
- Skill rules are immutable per project. Repositories that need to deviate publish a corresponding project rule under `$AGENT_SYSTEM_FOLDER/rules/`; precedence is resolved per `$AGENT_SYSTEM_SKILL/rules/conventions.md § Precedence Model`.

## Quick Reference Table Pattern

```markdown
## Quick Reference

<one-sentence framing of what the rule covers>

| Item | Rule |
|------|------|
| **<Concept>** | <Concise rule> |
| **<Concept>** | <Concise rule> |
```

The Quick Reference is the contract. If a rule does not appear there, it should not be a load-bearing rule. Detail and examples follow in the domain sections.
