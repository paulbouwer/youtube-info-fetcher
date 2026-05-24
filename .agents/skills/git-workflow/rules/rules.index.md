# Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the skill rules bundled under `$GIT_WORKFLOW_SKILL`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

The folder is flat (no category sub-folders); this index is therefore a file map.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Cross-cutting workflow principles: issue-first development, conventional commits, atomic changes, AI attribution, protected branches, provider detection | Authoring or reviewing any git-workflow operation (always loaded) |
| [`branch.md`](branch.md) | Branch structure, lifecycle, project policy reference | Running Create Branch |
| [`commit.md`](commit.md) | Commit message structure, grouping, AI attribution mechanics, signing configuration | Running Commit |
| [`pull-request.md`](pull-request.md) | PR structure, content requirements, issue linking, pre-PR validation | Running Create Pull Request |
| [`checklist.md`](checklist.md) | Consolidated validation checklist for all workflow operations | Validating any workflow output |
