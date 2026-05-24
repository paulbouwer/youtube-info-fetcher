# Git Workflow Core Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Invariant rules for git-based development workflows. These rules define the process and cannot be overridden by organisational policy.

## Fundamental Principles

### 1. Issue-First Development

All git artefacts MUST link to an issue per `$WORK_TRACKING_SKILL/rules/core.md` § Issue-First Development. The work-tracking skill owns the canonical statement of this principle; git-workflow consumes it.

| Artifact | Issue Requirement |
| -------- | ----------------- |
| Branch | MUST include issue ID in name |
| Commits | SHOULD reference issue context for grouping |
| Pull Request | MUST link to originating issue, AND the linked work package's `plan.md` `related:` frontmatter MUST be set (see `$WORK_TRACKING_SKILL/rules/work-package.md` § Issue Linkage and `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` § Draft Phase) |

### 2. Conventional Commits

All commits MUST follow the [Conventional Commits](https://www.conventionalcommits.org/) specification.

### 3. Atomic Changes

Each commit MUST represent a single logical change that can be independently understood and reverted.

### 4. AI Attribution

When AI coding agents contribute to changes, their contribution MUST be attributed using standard git trailers.

## Protected Branches

| Branch | Direct Commits | PR Required |
| ------ | -------------- | ----------- |
| `main` | Prohibited | Required |
| Feature branches | Allowed | N/A |

## Workflow Diagram

```text
┌─────────────────────────────────────────────────────────────────┐
│                        Git Workflow                             │
├─────────────────────────────────────────────────────────────────┤
│  1. Create Branch    →  Issue-linked, rules-aligned        │
│  2. Make Changes     →  Atomic commits, conventional format    │
│  3. Push Branch      →  Remote tracking, validation hooks      │
│  4. Create PR        →  Issue-linked, AI usage tracked         │
│  5. Review & Merge   →  Approval required, squash or merge     │
└─────────────────────────────────────────────────────────────────┘
```

## References

- [Conventional Commits Specification](https://www.conventionalcommits.org/)
- Policy rules in `$AGENT_SYSTEM_FOLDER/rules/git-workflow/`
- Issue-First Development principle: `$WORK_TRACKING_SKILL/rules/core.md` § Issue-First Development
- Work package linkage rules: `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md`
