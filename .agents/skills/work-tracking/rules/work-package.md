# Work Package Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Invariant rules for work package structure, required files, and lifecycle. Work packages are the local unit of planned work, paired 1:1 with a remote issue.

## Quick Reference

| Item | Rule |
| ---- | ---- |
| Location | `$PROJECT_FOLDER/work/` |
| Folder name | `YYYY-MM-DD-short-description` (lowercase, hyphens) |
| Required files | `spec.md`, `plan.md` |
| Recommended files | `verification-results.md`, `research/` |
| Issue link | `related:` frontmatter in `plan.md` (required before PR creation) |

## Principles

| Principle | Detail |
| --------- | ------ |
| Traceability | Every significant piece of work has a written specification and plan that can be referenced |
| Separation of intent and approach | The spec defines *what* and *why*; the plan defines *how* |
| Progressive documentation | Research informs the spec; the spec informs the plan; verification confirms the outcome |
| Linkage | Work packages link to their associated issue for cross-referencing (per `$WORK_TRACKING_SKILL/rules/core.md` § Bidirectional Linkage) |

## Folder Structure

```text
$PROJECT_FOLDER/work/
├── YYYY-MM-DD-short-description/
│   ├── spec.md                          # what and why
│   ├── plan.md                          # how (with related: frontmatter)
│   ├── verification-results.md          # verification of implementation
│   └── research/                        # supporting research and analysis
│       └── YYYY-MM-DD-topic.md
```

### Naming Convention

Work package folders use the format `YYYY-MM-DD-short-description`:

| Element | Requirement |
| ------- | ----------- |
| Date prefix | ISO 8601 date the work package was created |
| Description | Lowercase with hyphens, 3-7 words |

Good:

```text
2026-04-05-youtube-api-integration
2026-04-10-cli-command-structure
```

Bad:

```text
youtube-api-integration             (missing date)
20260405_youtube_api_integration     (compact date, underscores)
```

## Required Files

### spec.md — Specification

The spec captures the **problem**, **requirements**, and **success criteria**. It defines *what* needs to be done and *why*, without prescribing implementation details.

A spec typically includes:

| Section | Purpose |
| ------- | ------- |
| Problem statement | What needs to change and why |
| Requirements | What the implementation must satisfy |
| Out of scope | What is explicitly excluded |
| Success criteria | How to verify the work is complete |

### plan.md — Implementation Plan

The plan captures *how* the work will be carried out. It breaks the spec into actionable phases and tracks progress.

A plan typically includes:

| Section | Purpose |
| ------- | ------- |
| Approach | Brief summary of the approach |
| Phases | Trackable items broken into logical groups |
| Verification | Steps to confirm the implementation |
| Decisions | Key decisions made during planning or implementation |

#### Issue Linkage

The plan MUST link to its associated issue via YAML frontmatter once the issue exists:

```yaml
---
related: {issue-url}
---
```

The `related:` frontmatter MAY be absent or empty during the draft phase. The git-workflow `create-pull-request` action enforces presence at the PR-creation boundary.

## Recommended Files

### verification-results.md — Verification Log

A record of the verification of the implementation against the spec, the plan, and the issue's acceptance criteria. This creates an audit trail showing the work is complete.

A verification log typically includes:

| Section | Purpose |
| ------- | ------- |
| Verification step results | Results of each verification step from the plan |
| Success criteria status | Status of each success criterion from the spec |
| Acceptance criteria status | Status of each issue acceptance criterion |
| Gaps or follow-ups | Any items identified during verification |

### research/ — Research and Analysis

Supporting research, analysis, and investigation that informed the spec or plan. Research files use the naming convention `YYYY-MM-DD-topic.md`.

Good:

```text
research/2026-04-05-youtube-api-quota-limits.md
research/2026-04-05-cobra-vs-urfave-cli.md
```

## Lifecycle

```text
1. Create   ──▶  Folder + spec.md + plan.md (related: optional)
2. Specify  ──▶  Author spec.md
3. Research ──▶  Optional research/ files
4. Plan     ──▶  Author plan.md; create or link to issue (related: set)
5. Implement──▶  Update plan progress; tick task checkboxes
6. Verify   ──▶  Author verification-results.md
```

## Project Work Package Policy

Project-specific work package configuration is defined in `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md`. This includes:

- Frontmatter format and required keys
- `related:` enforcement boundary
- Section conventions for `spec.md` and `plan.md`
- Cross-references with decision records, learnings, and future-enhancement-ideas
