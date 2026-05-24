# Branch Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Structural rules for branch naming and lifecycle. These define the branch workflow mechanics that enable automated traceability across actions.

## Branch Structure

Branch names follow a structured format that encodes traceability information:

```text
{type}/{issue-id}-{description}
```

| Element | Requirement |
| ------- | ----------- |
| Type | Must be a valid type from project branch policy |
| Issue ID | Numeric identifier from the issue tracking system |
| Description | Lowercase alphanumeric with hyphens, starting with alphanumeric character |

Branch types MUST align with commit types. The allowed type set is defined in the project branch policy.

## Branch Lifecycle

```text
main ──▶ create branch ──▶ develop ──▶ push ──▶ PR ──▶ merge ──▶ delete branch
```

1. Branch from `main` (or designated base branch)
2. Develop with atomic commits
3. Push with upstream tracking
4. Create pull request
5. Merge after approval
6. Delete source branch

## Project Branch Policy

Project-specific branch configuration is defined in `$AGENT_SYSTEM_FOLDER/rules/git-workflow/branch.md`. This includes:

- Allowed branch types and their purposes
- Validation regex
- Valid and invalid examples
- Git hooks for branch name enforcement
