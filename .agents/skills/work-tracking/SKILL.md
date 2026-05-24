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

| Capability | Action | Description |
|------------|--------|-------------|
| Create Issue | `$WORK_TRACKING_SKILL/actions/create-issue.md` | Create a rules-compliant issue, add it to the configured board, and set initial status |
| Update Issue | `$WORK_TRACKING_SKILL/actions/update-issue.md` | Apply field-scoped or section-scoped non-destructive updates to an existing issue |
| Create Work Package | `$WORK_TRACKING_SKILL/actions/create-work-package.md` | Scaffold a rules-compliant work package; if an issue exists, append the back-link row |
| Update Work Package | `$WORK_TRACKING_SKILL/actions/update-work-package.md` | Apply field-scoped or section-scoped non-destructive updates to an existing work package |

### Bundled Rules

| Rule | File | Description |
|----------|------|-------------|
| Rules Index | `$WORK_TRACKING_SKILL/rules/rules.index.md` | Lazy-loading index for this skill's rules |
| Core | `$WORK_TRACKING_SKILL/rules/core.md` | Cross-cutting principles: Issue-First Development, complementary content models, 1:1 cardinality, bidirectional linkage, idempotency, lifecycle, status field |
| Issue | `$WORK_TRACKING_SKILL/rules/issue.md` | Invariant issue structure, body section layout per type, acceptance criteria format, Work Packages section |
| Work Package | `$WORK_TRACKING_SKILL/rules/work-package.md` | Invariant folder structure, required files, lifecycle, naming convention |
| Checklist | `$WORK_TRACKING_SKILL/rules/checklist.md` | Validation checklist for all work-tracking operations |

Actions load additional project rules from `$AGENT_SYSTEM_FOLDER/rules/work-tracking/` based on the capability being executed (issue policy, work package policy, templates, development environment).

## Flow

### Prerequisites

- [ ] Detect work tracking provider from `$WORK_TRACKING_PROVIDER` (root `AGENTS.md`)
- [ ] Verify provider authentication (e.g. `gh auth status` for GitHub)
- [ ] Confirm `$WORK_TRACKING_PROJECT` board is reachable

### Execution Steps

1. Load the bundled rules listed in the **Bundled Rules** section into context
2. Determine the user's intent and select the matching capability from the **Capabilities** section
3. Execute the selected capability — bundled rules are already in context; the action loads relevant project rules from `$AGENT_SYSTEM_FOLDER/rules/work-tracking/`
