# Work Tracking Checklist

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Consolidated validation checklist for all work-tracking operations. References the core, issue, and work-package rules for the underlying rules.

## Issue Creation

### Pre-Creation

Per `$WORK_TRACKING_SKILL/rules/core.md` § Issue-First Development and `$WORK_TRACKING_SKILL/rules/issue.md` § Issue Structure:

- [ ] Issue type identified (Feature, Bug, or Task)
- [ ] Title prepared per project issue policy format
- [ ] Body content prepared (Goal/Problem, Requirements/Expectations, Acceptance Criteria)
- [ ] Configured board (`$WORK_TRACKING_PROJECT`) is reachable

### Body Validation

Per `$WORK_TRACKING_SKILL/rules/issue.md` § Body Section Structure:

- [ ] Required sections present for the issue type
- [ ] No Tasks section in body
- [ ] Acceptance criteria use `AC{nnn}` numbering and checkbox format
- [ ] Work Packages section present (may be empty initially)

### Post-Creation

- [ ] Issue created in provider
- [ ] Issue added to configured board
- [ ] Initial status set to project default (e.g. Todo)

## Issue Update

### Pre-Update

Per `$WORK_TRACKING_SKILL/rules/core.md` § Idempotency of Updates:

- [ ] Target field or section identified
- [ ] Update is idempotent (re-run produces no change)
- [ ] Update is non-destructive to unrelated content

### Status Transition

Per `$WORK_TRACKING_SKILL/rules/core.md` § Status Field:

- [ ] Target status is one of the four standard values
- [ ] Transition reason is appropriate (implementation begins → In Progress; PR opened → Ready for Review)

### Acceptance Criteria Tick

- [ ] Criterion identified by `AC{nnn}` ID
- [ ] Underlying behaviour verified before ticking

## Work Package Creation

### Pre-Creation

Per `$WORK_TRACKING_SKILL/rules/work-package.md` § Folder Structure:

- [ ] Short description prepared (3-7 words, lowercase, hyphens)
- [ ] Today's ISO date determined
- [ ] Folder name matches `YYYY-MM-DD-short-description`
- [ ] No existing folder with the same name

### File Scaffold

Per `$WORK_TRACKING_SKILL/rules/work-package.md` § Required Files:

- [ ] `spec.md` created with required sections
- [ ] `plan.md` created with required sections
- [ ] `related:` frontmatter present in `plan.md` if issue exists; absent for drafts

### Linkage

Per `$WORK_TRACKING_SKILL/rules/core.md` § Bidirectional Linkage:

- [ ] If issue exists: row appended to issue's Work Packages table
- [ ] Back-link row appended idempotently (no duplicate)

## Work Package Update

### Pre-Update

Per `$WORK_TRACKING_SKILL/rules/core.md` § Idempotency of Updates:

- [ ] Target file and section identified
- [ ] Update is idempotent and non-destructive

### related: Update

- [ ] Issue URL is well-formed
- [ ] Issue exists at the URL
- [ ] If a previous `related:` value existed, the back-link row in the previous issue is removed

## Cross-Action

### PR Boundary

Per `$WORK_TRACKING_SKILL/rules/core.md` § Issue-First Development:

- [ ] When git-workflow `create-pull-request` runs, the work package linked to the current branch (if any) has a non-empty, non-TBD `related:` value
- [ ] If `related:` is missing, PR creation is blocked until resolved

## Quick Reference

| Operation | Key Validation |
| --------- | -------------- |
| Create Issue | Type valid, body sections correct, board placement set |
| Update Issue | Idempotent, section-scoped, status transition appropriate |
| Create Work Package | Folder name correct, required files scaffolded, back-link applied |
| Update Work Package | Idempotent, section-scoped, `related:` well-formed |
