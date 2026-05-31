---
name: work-tracking
description: Manage work tracking across local work packages and remote issues, including creation, linking, and updates
---

# Work Tracking Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_PROVIDER` and `$WORK_TRACKING_PROJECT` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

This skill provides capabilities for managing work tracking artefacts that comply with organisational rules. It owns both sides of the work-tracking lifecycle — local work packages in `$PROJECT_FOLDER/work/` and remote issues on the configured tracking provider — and ensures bidirectional linkage between them.

## Configuration

### Capabilities

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|
| Create Issue | `$WORK_TRACKING_SKILL/actions/create-issue.md` | Create a rules-compliant issue, add it to the configured board, and set initial status | User wants to create a new issue / unit of tracked work on the provider |
| Update Issue | `$WORK_TRACKING_SKILL/actions/update-issue.md` | Apply field-scoped or section-scoped non-destructive updates to an existing issue | User wants to edit, tick acceptance criteria in, or append to an existing issue |
| Create Work Package | `$WORK_TRACKING_SKILL/actions/create-work-package.md` | Scaffold a rules-compliant work package; if an issue exists, append the back-link row | User wants to start a new unit of planned local work |
| Update Work Package | `$WORK_TRACKING_SKILL/actions/update-work-package.md` | Apply field-scoped or section-scoped non-destructive updates to an existing work package | User wants to edit, tick tasks in, or append to an existing work package |

### Rules

| Index | File |
|-------|------|
| Skill Rules Index | `$WORK_TRACKING_SKILL/rules/rules.index.md` |
| Project Rules Index | `$AGENT_SYSTEM_FOLDER/rules/work-tracking/rules.index.md` |

Consult each index and load only the rule files whose **Load when** matches the selected capability.

## Flow

### Prerequisites

- [ ] Detect work tracking provider from `$WORK_TRACKING_PROVIDER` (root `AGENTS.md`)
- [ ] Verify provider authentication (e.g. `gh auth status` for GitHub)
- [ ] Confirm `$WORK_TRACKING_PROJECT` board is reachable

### Execution Steps

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. Load skill and project rules via the indexes listed in the **Rules** section — only the rules whose **Load when** matches the selected capability
3. Execute the selected capability
