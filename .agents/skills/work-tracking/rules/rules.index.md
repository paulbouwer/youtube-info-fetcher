# Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the skill rules bundled under `$WORK_TRACKING_SKILL`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

The folder is flat (no category sub-folders); this index is therefore a file map.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Cross-cutting principles: Issue-First Development, complementary content models, 1:1 cardinality, bidirectional linkage, idempotency, lifecycle, status field | Authoring or reviewing any work-tracking operation (always loaded) |
| [`issue.md`](issue.md) | Invariant issue structure, body section layout per type, acceptance criteria format, Work Packages section | Authoring or reviewing an issue (Create Issue, Update Issue) |
| [`work-package.md`](work-package.md) | Invariant folder structure, required files, lifecycle, naming convention | Authoring or reviewing a work package (Create Work Package, Update Work Package) |
| [`checklist.md`](checklist.md) | Consolidated validation checklist for all work-tracking operations | Validating any work-tracking output |
