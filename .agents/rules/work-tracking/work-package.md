# Work Package Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

Project-specific work package configuration. Customizable bits sit on top of the invariant structure defined in `$WORK_TRACKING_SKILL/rules/work-package.md`.

## Frontmatter

The `plan.md` file uses YAML frontmatter for issue linkage:

```yaml
---
related: {issue-url}
---
```

| Key | Type | Requirement |
| --- | ---- | ----------- |
| `related` | string (URL) | REQUIRED before pull-request creation; MAY be absent or empty during draft phase |

The `related:` value MUST be the full issue URL for the configured `$WORK_TRACKING_PROVIDER` (e.g. `https://github.com/{owner}/{repo}/issues/{n}`).

## Draft Phase

A work package is a *draft* while `related:` is absent, empty, or set to `TBD`. Drafts are permitted to support planning-first workflows where the issue takes shape during the planning session.

| Boundary | Enforcement |
| -------- | ----------- |
| Work package creation | Draft permitted |
| `update-work-package` setting `related:` | Promotes draft to linked |
| Pull request creation | git-workflow `create-pull-request` blocks if `related:` is unset, empty, or `TBD`, OR if `verification-results.md` is missing |

## File Section Conventions

### spec.md Sections

| Heading | Required | Purpose |
| ------- | -------- | ------- |
| `## Problem` | Yes | What needs to change and why |
| `## Requirements` | Yes | What the implementation must satisfy |
| `## Out of Scope` | Recommended | What is explicitly excluded |
| `## Success Criteria` | Yes | How to verify the work is complete |

### plan.md Sections

| Heading | Required | Purpose |
| ------- | -------- | ------- |
| `## Approach` | Yes | Brief summary of the approach |
| `## Phases` | Yes | Trackable items broken into logical groups, with task checkboxes |
| `## Verification` | Recommended | Steps to confirm the implementation |
| `## Decisions` | Recommended | Key decisions made during planning or implementation |

Task checkboxes in `## Phases` use no specific numbering scheme — they are implementation detail and are agent-managed during execution.

### verification-results.md Sections

`verification-results.md` MUST exist before pull-request creation (see `$AGENT_SYSTEM_FOLDER/rules/git-workflow/pull-request.md` § Pre-PR Validation). It records the author's own verification of the implementation against the linked issue.

| Heading | Required | Purpose |
| ------- | -------- | ------- |
| `## Summary` | Yes | Brief overview of what was verified |
| `## Acceptance Criteria` | Yes | Table mapping each AC from the linked issue to a Pass/Fail status with evidence |
| `## Plan Coverage` | Yes | Confirmation that every phase / task in `plan.md` was completed (or note explicit deferrals) |
| `## Decisions Realised` | Recommended | Decisions captured during implementation, including links to any decision records authored |
| `## Follow-ups` | Recommended | Items intentionally deferred to a later session, with rationale |

The file MAY include a `related:` frontmatter pointing at the issue for symmetry with `plan.md`.

## Cross-References

Work packages complement other project documentation. Cross-references to these artefacts are recommended where relevant:

| Artefact | Folder | When to Reference |
| -------- | ------ | ----------------- |
| Decision records | `$PROJECT_FOLDER/decision-records/` | When a significant architectural or technical decision is made during the work |
| Learnings | `$PROJECT_FOLDER/learnings.md` | When the work surfaces a cross-cutting pattern worth recording |
| Future enhancement ideas | `$PROJECT_FOLDER/future-enhancement-ideas.md` | When the work surfaces ideas beyond its own scope |

## References

- Skill invariants: `$WORK_TRACKING_SKILL/rules/work-package.md`
- Issue policy: `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md`
