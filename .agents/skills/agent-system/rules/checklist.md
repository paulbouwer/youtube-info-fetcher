# Checklist Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL`, `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring a skill-bundled validation checklist file. Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`. This rule documents the *checklist asset pattern* — separate from `$AGENT_SYSTEM_SKILL/rules/validation.md`, which is this skill's own validation checklist instance.

| Item | Rule |
|------|------|
| **Path** | `$AGENT_SYSTEM_FOLDER/skills/<domain>/rules/checklist.md` (or `validation.md` where the skill uses that name) |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/checklist.md` |
| **Reference implementation** | `$DECISION_RECORDS_SKILL/rules/checklist.md` |
| **Required H2 sections** | Variables, Overview, then validation groups |
| **Item format** | `- [ ]` markdown task-list items |
| **Heading cap** | H3 |

## Purpose

A checklist is a single-pass validation tool used by a skill's `review` action. It references the rules authority (`core.md` or equivalent) — it does not restate the rules. Restating rules in two places creates a drift surface and forces dual maintenance.

## File Structure

| Section | Required | Purpose |
|---------|----------|---------|
| `## Variables` | Yes | Inheritance declaration |
| `## Overview` | Yes | One-sentence purpose plus pointer to the rules authority |
| Validation groups | Yes | Grouped checklist items, each group cross-referencing a section of the rules authority |

## Rules

- Each group references the specific section of the rules authority it validates using `§` notation: `$<SKILL>_SKILL/rules/core.md § Required Sections`.
- Checklist items use `- [ ]` format. Nested sub-items are permitted only when the parent item's correctness depends on the sub-items (e.g. each clause of a structured statement).
- Never restate rules from the rules authority — reference them. The checklist's job is verifiability, not re-specification.
- Keep groups flat (no deeply nested sub-checklists beyond the natural breakdown of a single item).
- Group order SHOULD follow the order of the corresponding sections in the rules authority.

## Overview Pattern

```markdown
## Overview

Single-pass validation for a completed <asset>. Rules are defined in `$<SKILL>_SKILL/rules/<rules-authority>.md`; this checklist provides a quick reference for verification.
```

## Validation Group Pattern

```markdown
## <Group Name>

Validate against `$<SKILL>_SKILL/rules/<rules-authority>.md § <Section Name>`:

- [ ] **<Element>** — <verifiable check>
- [ ] **<Element>** — <verifiable check>
```

Each item MUST be verifiable by inspection or a deterministic check. Items phrased as "ensure quality" or "looks correct" are non-verifiable and non-conformant.
