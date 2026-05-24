# Pull Request Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Structural rules for pull request creation. These define the PR workflow mechanics that ensure consistency, traceability, and quality.

## PR Structure

Every pull request has two components:

| Component | Requirement |
| --------- | ----------- |
| Title | MUST follow conventional commit format, ≤72 characters |
| Description | MUST include required sections per project PR policy |

### Title Format

PR titles follow the conventional commit format:

```text
{type}({scope}): {description}
```

The type is derived from the branch prefix. The allowed types are defined in the project PR policy.

## Content Requirements

| Section | Requirement |
| ------- | ----------- |
| Description | MUST include overview of changes |
| Related Issues | MUST link to originating issue |
| Changes | MUST list modifications |
| AI Usage | MUST include if commits have AI trailers |

## Issue Linking

Every pull request MUST be linked to an issue:

1. Extract issue ID from branch name
2. Include issue reference in PR description
3. Use closing keywords when appropriate (`Fixes #123`, `Closes #456`)

## Pre-PR Validation

Before PR creation, validate:

| Condition | Requirement |
| --------- | ----------- |
| Branch pushed | Upstream tracking must be configured |
| Commits exist | Changes must exist between source and target |

## Project PR Policy

Project-specific PR configuration is defined in `$AGENT_SYSTEM_FOLDER/rules/git-workflow/pull-request.md`. This includes:

- Allowed title types and format examples
- Description structure and style (e.g. What/Why pattern)
- AI usage tracking rules and thresholds
- Description character limits (varies by provider)
