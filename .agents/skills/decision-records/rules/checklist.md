# Decision Record Validation Checklist

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Single-pass validation for a completed decision record. Rules are defined in `$DECISION_RECORDS_SKILL/rules/core.md`; this checklist provides a quick reference for verification.

## Structure and Content

Validate against the required sections in `$DECISION_RECORDS_SKILL/rules/core.md` § Required Sections and the document structure in `$DECISION_RECORDS_SKILL/templates/decision-record.md`:

- [ ] **Title** — Clear, descriptive name present
- [ ] **Metadata** — Status, deciders, date (DD MMMM YYYY), related docs
- [ ] **Y-Statement** — All five clauses present, specific, and coherent:
  - [ ] "In the context of" — situation is specific, not vague
  - [ ] "facing" — constraint is clear
  - [ ] "we decided" — decision is concrete
  - [ ] "to achieve" — outcome is measurable
  - [ ] "accepting" — trade-off is honest
- [ ] **Context** — Explains why the decision was needed; constraints documented
- [ ] **Decision** — Choice stated with rationale
- [ ] **Consequences** — At least one benefit and at least one risk
- [ ] **Alternatives** — At least one genuine alternative with pros, cons, and rejection reason

## Naming and Location

Validate against `$DECISION_RECORDS_SKILL/rules/core.md` § Naming Convention:

- [ ] File located in `$PROJECT_FOLDER/decision-records/`
- [ ] Filename date: `YYYY-MM-DD` (ISO 8601)
- [ ] Metadata date: `DD MMMM YYYY`
- [ ] Description: lowercase, hyphens only, 3-7 words
- [ ] No spaces or underscores in filename

## Status

Validate against `$DECISION_RECORDS_SKILL/rules/core.md` § Status Lifecycle:

- [ ] Status is one of: `Proposed`, `Accepted`, `Deprecated`, `Superseded`
- [ ] If `Superseded`, link to replacement record is provided
- [ ] If `Deprecated`, record has been moved to `$PROJECT_FOLDER/decision-records/deprecated/`

## Actionability

- [ ] Alternatives are genuinely different options (not fabricated)
- [ ] Someone could implement or follow this decision based on the record alone
