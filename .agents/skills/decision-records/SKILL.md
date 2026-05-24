---
name: decision-records
description: Create and review architectural decision records
---

# Decision Records Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

This skill provides capabilities for creating and reviewing decision records through guided discovery and rules compliance.

## Configuration

### Capabilities

| Capability | Action | Description |
|------------|--------|-------------|
| Create | `$DECISION_RECORDS_SKILL/actions/create.md` | Guide the creation of a decision record through Socratic discovery |
| Review | `$DECISION_RECORDS_SKILL/actions/review.md` | Analyse an existing decision record for completeness and quality |

### Bundled Rules

| Rule | File | Description |
|----------|------|-------------|
| Rules Index | `$DECISION_RECORDS_SKILL/rules/rules.index.md` | Lazy-loading index for this skill's rules |
| Core | `$DECISION_RECORDS_SKILL/rules/core.md` | When to create, naming conventions, required sections, status lifecycle |
| Checklist | `$DECISION_RECORDS_SKILL/rules/checklist.md` | Validation checklist for decision record completeness |

### Bundled Templates

| Template | File | Description |
|----------|------|-------------|
| Decision Record | `$DECISION_RECORDS_SKILL/templates/decision-record.md` | Decision record markdown template |

### Index Maintenance

Both capabilities are responsible for maintaining the decision records index at `$PROJECT_FOLDER/decision-records/decision-records.index.md`:

| Intent | Action |
|---------|--------|
| Create | Add new entry to **Active Records** table. Format: `\| [filename.md](filename.md) \| Status \| Y-Statement \|`. Remove placeholder row if this is the first entry. |
| Review (status change) | Update the status column in the **Active Records** table |
| Review (deprecation or supersession) | Move the file to `$PROJECT_FOLDER/decision-records/deprecated/`, remove from **Active Records**, add to **Deprecated Records** with status, Y-Statement, and superseding record link if applicable |

## Flow

### Prerequisites

- [ ] Verify `$PROJECT_FOLDER/decision-records/` directory exists
- [ ] Verify `$PROJECT_FOLDER/decision-records/decision-records.index.md` is accessible

### Execution Steps

1. Load the bundled rules listed in the **Bundled Rules** section into context
2. Load the bundled templates listed in the **Bundled Templates** section into context
3. Determine the user's intent and select the matching capability from the **Capabilities** section
4. Execute the selected capability — rules and templates are already in context
5. Update the decision records index as per the rules in the **Index Maintenance** section

