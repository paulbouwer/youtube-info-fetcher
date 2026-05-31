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

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|
| Create | `$DECISION_RECORDS_SKILL/actions/create.md` | Guide the creation of a decision record through Socratic discovery | User wants to capture or document a new decision |
| Review | `$DECISION_RECORDS_SKILL/actions/review.md` | Analyse an existing decision record for completeness and quality | User wants to review, score, or update the status of an existing decision record |

### Rules

| Index | File |
|-------|------|
| Skill Rules Index | `$DECISION_RECORDS_SKILL/rules/rules.index.md` |

Consult each index and load only the rule files whose **Load when** matches the selected capability.

### Bundled Templates

| Template | File | Load when |
|----------|------|-----------|
| Decision Record | `$DECISION_RECORDS_SKILL/templates/decision-record.md` | Running the Create capability (generating a new record) |

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

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. Load skill rules via the **Rules** index — only the rules whose **Load when** matches the selected capability
3. Load bundled templates whose **Load when** matches the selected capability
4. Execute the selected capability
5. Update the decision records index as per the rules in the **Index Maintenance** section

