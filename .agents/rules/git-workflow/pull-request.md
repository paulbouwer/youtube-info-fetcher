# Pull Request Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

Project-specific pull request configuration. Defines title format, description structure, AI usage tracking, and character limits for this project.

## PR Title Format

Pull request titles follow the conventional commit format:

```text
{type}({scope}): {description}
```

| Element | Requirement |
| ------- | ----------- |
| Type | Must match branch type: `feat`, `fix`, `core`, `docs`, `refactor`, `test` |
| Scope | Optional, matches affected area |
| Description | Brief summary, imperative mood |

### Examples

```text
feat(auth): add JWT token validation
fix(parser): handle null input gracefully
docs(api): update endpoint documentation
core(ci): add automated testing pipeline
```

## PR Description Structure

### Required Sections

| Section | Purpose | Required |
| ------- | ------- | -------- |
| Description | Brief overview of changes | Yes |
| Related Issues | Link to work items | Yes |
| Changes | Detailed list of modifications | Yes |
| Additional Context | Extra information | Optional |
| Coding Agents Used | AI contribution tracking | Conditional |

### What/Why Pattern

Each change should explain:

- **What**: Brief description of what was changed
- **Why**: Reason for the change

## Issue Linking

Extract issue ID from branch name:

```text
feat/123-add-widget  →  Issue #123
fix/PROJ-456-fix-bug →  Issue PROJ-456
```

Use closing keywords to automatically close issues on merge: `Fixes #123`, `Closes #456`, `Resolves #789`.

## Pre-PR Validation

The following checks MUST pass before a pull request is opened. The git-workflow `create-pull-request` action enforces them and blocks PR creation otherwise.

| Check | Requirement | Resolution if Missing |
| ----- | ----------- | --------------------- |
| Work package `related:` frontmatter | If a work package exists for this branch's issue, its `plan.md` MUST have a non-empty, non-`TBD` `related:` URL pointing at the issue | Run the work-tracking skill's `update-work-package` action to set `related:` |
| Work package `verification-results.md` | If a work package exists for this branch's issue, the work package folder MUST contain a `verification-results.md` file documenting the outcome of the implementation against the issue's acceptance criteria | Author `verification-results.md` per `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` § verification-results.md Sections before retrying PR creation |

The `verification-results.md` check exists because verification is part of the implementation, not a post-merge afterthought: the PR reviewer should be able to see the author's own verification of the acceptance criteria at review time.

## AI Agent Usage Tracking

### When to Include

Include the "Coding Agents Used" section when git commits contain agent/model trailers and including the section keeps the total description within the character limit.

### When to Comment Instead

Post as a PR comment when including the section would exceed the description character limit or when a detailed breakdown is needed.

### Usage Table Format

```markdown
| Agent | Model | Commits |
| ----- | ----- | ------- |
| github-copilot | gpt-4o | 5 |
| cursor | claude-opus-4-5 | 3 |
```

## Character Limits

| Element | Limit | Notes |
| ------- | ----- | ----- |
| Title | 72 characters | Convention |
| Description | 4000 characters | Azure DevOps limit; GitHub allows ~65K |

## References

- [Branch Policy](./branch.md)
- [Commit Policy](./commit.md)
