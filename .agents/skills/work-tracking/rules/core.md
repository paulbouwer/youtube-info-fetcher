# Work Tracking Core Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Invariant rules for work tracking across both local artefacts (work packages) and remote artefacts (issues on the configured tracking provider). These rules define the process and cannot be overridden by organisational policy.

## Fundamental Principles

### 1. Issue-First Development

All work MUST be traceable to an issue in the configured tracking system (`$WORK_TRACKING_PROVIDER`). The issue is the canonical, project-visible unit of trackable work.

| Artefact | Issue Requirement |
| -------- | ----------------- |
| Work package | MUST link to its issue via `related:` frontmatter (enforced at PR-creation boundary) |
| Branch | MUST include issue ID in name (per git-workflow branch policy) |
| Commits | SHOULD reference issue context for grouping |
| Pull request | MUST link to originating issue |

The `git-workflow` skill defers to this principle and references it from `$GIT_WORKFLOW_SKILL/rules/core.md`.

### 2. Provider-Neutral Vocabulary

Two terms are used across all work-tracking rules regardless of provider:

| Term | Meaning | Provider Mapping |
| ---- | ------- | ---------------- |
| **Issue** | The remote unit of trackable work | GitHub Issue, Azure DevOps Work Item |
| **Board** | The remote view that organises issues by status | GitHub Project (v2), Azure DevOps Board |

### 3. Complementary Content Models

The issue and the work package carry **complementary** content, not duplicated content.

| Artefact | Owns | Audience |
| -------- | ---- | -------- |
| Issue | Goal/Problem, Requirements/Expectations, Acceptance Criteria | Project-visible (PM, stakeholders) |
| Work package (`spec.md`, `plan.md`) | Research, detailed implementation plan, phase-level tasks | Implementation-visible (agent, contributor) |

There is no automated sync between issue body and work package files. Tasks live in `plan.md` only; acceptance criteria live in the issue only.

### 4. Strict 1:1 Cardinality

Each work package links to exactly one issue, and each issue is linked to at most one work package. Sub-issues and multi-package issues are out of scope.

### 5. Bidirectional Linkage

| Direction | Mechanism |
| --------- | --------- |
| Local → Remote | `related:` frontmatter in `plan.md` pointing at the issue URL |
| Remote → Local | A row in the issue's "Work Packages" table naming the work package folder |

Both links are populated by the work-tracking actions; agents MUST NOT hand-edit them outside those actions.

### 6. Idempotency of Updates

`update-issue` and `update-work-package` MUST be idempotent. Re-running an update with the same input MUST be a no-op. Updates MUST be section-scoped (target a specific named section) and non-destructive (preserve all unrelated content).

## Lifecycle

```text
┌────────────────────────────────────────────────────────────────┐
│                    Work Tracking Lifecycle                     │
├────────────────────────────────────────────────────────────────┤
│  1. Plan              →  Optional: draft work package first   │
│  2. Create Issue      →  Goal, Requirements, Acceptance       │
│                          Criteria; added to board as Todo     │
│  3. Create / Link     →  Work package linked to issue via     │
│     Work Package         related: frontmatter; back-link row  │
│                          appended to issue's Work Packages    │
│                          table                                │
│  4. Implement         →  Branch + commits + plan.md ticking   │
│     (status: In Progress, set via update-issue)               │
│  5. Open PR           →  related: enforced; status flips to   │
│     Ready for Review (set via update-issue)                   │
│  6. Merge             →  Fixes #N closes issue; board moves   │
│                          to Done automatically                │
└────────────────────────────────────────────────────────────────┘
```

## Status Field

The board status field has four standard values. Names are customizable per project in `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md`.

| Status | Set By | Trigger |
| ------ | ------ | ------- |
| Todo | `create-issue` | Issue creation |
| In Progress | `update-issue` | Implementation begins (typically first commit on feature branch) |
| Ready for Review | `update-issue` | Pull request opened |
| Done | Provider-native | Issue closed via PR merge (`Fixes #N`) |

## References

- Project policy: `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md`
- Project policy: `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md`
- Git workflow integration: `$GIT_WORKFLOW_SKILL/rules/core.md` § Issue-First Development
