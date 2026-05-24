# Issue Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_PROVIDER` and `$WORK_TRACKING_PROJECT` from root `AGENTS.md`

## Overview

Project-specific issue configuration. Defines the title format, type set, type-to-branch mapping, board status names, and section heading strings for this project.

## Issue Types

This project uses the following issue types:

| Type | Purpose | Body Template | Label |
| ---- | ------- | ------------- | ----- |
| Feature | A new capability, request, or addition | `templates/feature.md` | `Feature` |
| Bug | An incorrect behaviour or unexpected problem | `templates/bug.md` | `Bug` |
| Task | Operational, structural, or non-feature repository work | `templates/task.md` | `Task` |

### Type Conveyance

This project conveys issue type via **labels**, not GitHub's native issue-type mechanism.

**Rationale:** native issue types are only available on organisation-owned repositories. This project lives under a personal account, so labels are used as the single, repo-wide mechanism for conveying type.

Each issue template carries a `type:` frontmatter key whose value is one of `Feature`, `Bug`, `Task`. The work-tracking actions read this value and apply the matching label when creating the issue (e.g. `gh issue create --label Task ...`). The `--type` flag is not used on this repository.

The label set MUST exist in the repository with names matching the type vocabulary above (case-sensitive).

## Title Format

Issue titles use a single-line, sentence-case statement of the desired outcome:

```text
{Outcome statement in imperative or descriptive form}
```

Good:

```text
Bootstrap repository with development environment and agent system
Handle null input gracefully in transcript parser
Add Docusaurus website for the YouTube Info Fetcher handbook
```

Bad:

```text
feat: add transcript parsing                  (conventional-commit prefix not used in titles)
new feature                                   (vague)
TRANSCRIPT PARSING NOT WORKING                (uppercase shouting)
```

| Element | Requirement |
| ------- | ----------- |
| Length | ≤ 100 characters |
| Case | Sentence case |
| Mood | Imperative for Feature/Task; descriptive for Bug |
| Punctuation | No trailing period |

## Type to Branch Mapping

Issue type maps to a default branch type (per the git-workflow branch policy in `$AGENT_SYSTEM_FOLDER/rules/git-workflow/branch.md`). The mapping is *intent-driven*: the default applies unless the change's intent suggests an alternative.

| Issue Type | Default Branch Type | Alternatives (by intent) |
| ---------- | ------------------- | ------------------------ |
| Bug | `fix` | — |
| Feature | `feat` | `docs`, `refactor`, `test` if the work on a feature is documentation, restructuring, or testing only |
| Task | `core` | `docs`, `refactor` for repo-level documentation or restructuring |

`create-branch` reads the linked issue's type and proposes the default; for Feature and Task issues the agent SHOULD confirm with the user whether an alternative branch type is more appropriate based on the change's intent.

## Board Status

The board status field on `$WORK_TRACKING_PROJECT` has these values:

| Status | When Set | Set By |
| ------ | -------- | ------ |
| `Todo` | At issue creation | `create-issue` |
| `In Progress` | When implementation begins (typically first commit on the feature branch) | `update-issue` |
| `Ready for Review` | When pull request is opened | `update-issue` (later automatable by git-workflow `create-pull-request`) |
| `Done` | When the issue closes via PR merge with `Fixes #N` | Provider-native automation |

The exact field name on the board is `Status`. Status value strings MUST match the project's board configuration exactly (case-sensitive).

## Section Headings

Issue body section headings are template-stable strings used for section-scoped non-destructive edits by `update-issue` and `update-work-package`.

| Section | Heading String |
| ------- | -------------- |
| Goal (Feature/Task) | `### 🎯 Goal` |
| Problem (Bug) | `### 👎 Problem` |
| Requirements (Feature/Task) | `### 🤔 Requirements` |
| Expectations (Bug) | `### ✅ Expectations` |
| Acceptance Criteria | `### 🎖️ Acceptance Criteria` |
| Work Packages | `### 📋 Work Packages` |

These headings are also the parsing anchors used by actions to locate sections for non-destructive edits.

## Acceptance Criteria Authoring

| Rule | Detail |
| ---- | ------ |
| Numbering | `AC001`, `AC002`, ... — zero-padded to three digits |
| Format | `- [ ] **AC{nnn}**: {verifiable statement}` |
| Verifiability | Each criterion MUST describe an observable, testable outcome |
| Tense | Present tense, declarative ("X is true after Y") |

Good:

```markdown
- [ ] **AC001**: `youtube-info-fetcher transcript {videoId}` writes the transcript to stdout when the video has captions
- [ ] **AC002**: The CLI exits with status code 2 and prints a clear error when the video has no captions
```

Bad:

```markdown
- [ ] Make sure the transcript thing works                 (vague, no AC ID)
- [ ] AC003 — implementation should be fast               (not verifiable)
```

## Work Packages Table Format

```markdown
### 📋 Work Packages

| Work Package | Description |
| ------------ | ----------- |
| {folder} | {one-line description} |
```

| Rule | Detail |
| ---- | ------ |
| Work Package column | Bare work package folder name (no markdown link) |
| Description column | One-line summary of the work package's purpose |
| Idempotency | A row whose Work Package column matches an existing row MUST NOT be added again |

## Character Limits

| Element | Limit | Notes |
| ------- | ----- | ----- |
| Title | 100 characters | Sentence-case outcome statement |
| Body | 65,000 characters | GitHub provider limit; revisit for other providers |

## References

- Issue templates: `$AGENT_SYSTEM_FOLDER/rules/work-tracking/templates/`
- Branch types: `$AGENT_SYSTEM_FOLDER/rules/git-workflow/branch.md`
- Skill core: `$WORK_TRACKING_SKILL/rules/core.md`
- Skill issue invariants: `$WORK_TRACKING_SKILL/rules/issue.md`
