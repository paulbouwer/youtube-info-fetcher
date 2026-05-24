# Issue Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Invariant rules for issue structure, body sections, and lifecycle. These define the issue mechanics that ensure consistency and traceability across all providers.

## Issue Structure

Every issue has the following components:

| Component | Requirement |
| --------- | ----------- |
| Title | MUST follow the format defined in project issue policy |
| Type | MUST be one of: Feature, Bug, Task |
| Body | MUST follow the per-type body section structure (see below) |
| Board placement | MUST be added to the configured project board on creation |
| Initial status | MUST be set to the project's default initial status on creation |

## Body Section Structure

Issue bodies have a fixed, type-specific section layout. Section ordering and headings are invariant; section *content* is authored per template.

### Feature and Task Issues

| Section | Purpose |
| ------- | ------- |
| Goal | One-paragraph statement of what the work delivers |
| Requirements | Bulleted list of what must be true for the goal to be met |
| Acceptance Criteria | Numbered checkbox list (`AC001`, `AC002`, ...) of verifiable conditions |
| Work Packages | Table linking to the associated work package(s) |

### Bug Issues

| Section | Purpose |
| ------- | ------- |
| Problem | Describes what is wrong |
| Expectations | Describes the correct behaviour |
| Acceptance Criteria | Numbered checkbox list of verifiable conditions for the fix |
| Work Packages | Table linking to the associated work package(s) |

### Tasks Section

Issue bodies MUST NOT contain a Tasks section. Detailed implementation tasks live in the work package's `plan.md` only.

## Acceptance Criteria

| Rule | Detail |
| ---- | ------ |
| Numbering | `AC001`, `AC002`, ... — zero-padded to three digits |
| Format | `- [ ] **AC{nnn}**: {verifiable statement}` |
| Verification | Each criterion MUST be independently verifiable |
| Updates | Ticked via `update-issue` as criteria are met during implementation/verification |

## Work Packages Section

This section is the canonical back-link from issue to work package. It is populated by `create-work-package` and updated by `update-work-package`.

| Rule | Detail |
| ---- | ------ |
| Format | Markdown table with columns: Work Package, Description |
| Work Package column | Bare work package folder name (no markdown link) |
| Description column | Brief one-line description of the work package's purpose |
| Idempotency | Re-running creation with the same work package MUST NOT add a duplicate row |
| Section heading | Exact heading string is defined in project issue policy and MUST match across all templates |

## Lifecycle

```text
created (Todo) ──▶ in progress ──▶ ready for review ──▶ done (closed)
```

Status transitions are described in `$WORK_TRACKING_SKILL/rules/core.md` § Status Field.

## Project Issue Policy

Project-specific issue configuration is defined in `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md`. This includes:

- Title format and conventions
- Type → branch type mapping (intent-driven)
- Board status field name and exact value strings
- Configured board name (`$WORK_TRACKING_PROJECT`)
- Acceptance criteria authoring conventions
- Work Packages section heading string
