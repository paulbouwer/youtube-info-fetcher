# Git Workflow Checklist

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Consolidated validation checklist for all git workflow operations.

## Branch Creation

### Pre-Creation

Per `$GIT_WORKFLOW_SKILL/rules/core.md` § Issue-First Development and `$GIT_WORKFLOW_SKILL/rules/branch.md`:

- [ ] Issue exists in tracking system
- [ ] Issue ID identified
- [ ] Branch type determined (per allowed types in branch policy)
- [ ] Branch description prepared (lowercase, hyphens)

### Validation

Per `$GIT_WORKFLOW_SKILL/rules/branch.md` § Branch Structure and `$AGENT_SYSTEM_FOLDER/rules/git-workflow/branch.md`:

- [ ] Branch name matches naming pattern
- [ ] Branch name passes validation regex
- [ ] Branch created from correct base (`main` or designated branch)
- [ ] Upstream tracking configured

### Post-Creation

- [ ] Branch pushed to remote

## Commits

### Pre-Commit

Per `$GIT_WORKFLOW_SKILL/rules/core.md` § Atomic Changes and `$GIT_WORKFLOW_SKILL/rules/commit.md` § Commit Grouping:

- [ ] Changes are atomic (single logical change)
- [ ] Changes are related (same feature/bug/task)
- [ ] Commit type identified
- [ ] Commit scope identified (if applicable)

### Message Validation

Per `$GIT_WORKFLOW_SKILL/rules/commit.md` § Message Structure and `$AGENT_SYSTEM_FOLDER/rules/git-workflow/commit.md`:

- [ ] Type is valid (per allowed types in commit policy)
- [ ] Subject line ≤50 characters
- [ ] Subject uses imperative mood
- [ ] Subject starts lowercase after colon
- [ ] No period at end of subject
- [ ] Body lines ≤72 characters (if body present)

### AI Attribution

Per `$GIT_WORKFLOW_SKILL/rules/core.md` § AI Attribution and `$GIT_WORKFLOW_SKILL/rules/commit.md` § AI Attribution:

- [ ] If AI contributed: `Co-authored-by` trailer present
- [ ] If AI contributed: `agent` trailer present
- [ ] If AI contributed: `model` trailer present
- [ ] No partial trailer sets (all or none)

### Signing

Per `$GIT_WORKFLOW_SKILL/rules/commit.md` § Signing and `$AGENT_SYSTEM_FOLDER/rules/git-workflow/commit.md`:

- [ ] Commit signing configured (GitHub only)
- [ ] Commit is signed (`git log --show-signature -1`)

## Pull Request Creation

### Pre-PR Validation

Per `$GIT_WORKFLOW_SKILL/rules/core.md` § Issue-First Development and `$GIT_WORKFLOW_SKILL/rules/pull-request.md`:

- [ ] All commits follow conventional format
- [ ] Branch pushed with upstream tracking
- [ ] Issue ID extracted from branch name
- [ ] Issue context retrieved (title, description)
- [ ] Linked work package `plan.md` has `related:` frontmatter set (non-empty, not `TBD`) per `$WORK_TRACKING_SKILL/rules/core.md` § Issue-First Development and `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` § Draft Phase

### PR Content

Per `$GIT_WORKFLOW_SKILL/rules/pull-request.md` § Content Requirements and `$AGENT_SYSTEM_FOLDER/rules/git-workflow/pull-request.md`:

- [ ] Title follows conventional commit format
- [ ] Title ≤72 characters
- [ ] Description includes overview
- [ ] Related issue linked with closing keyword
- [ ] Changes section lists modifications
- [ ] AI usage section included (if commits have AI trailers)
- [ ] Total description ≤4000 characters

### Post-Creation

- [ ] PR created in provider
- [ ] Reviewers assigned (if required)

## Quick Reference

| Operation | Key Validation |
| --------- | -------------- |
| Branch | Naming pattern, issue-linked, upstream tracking |
| Commit | Conventional format, atomic changes, AI trailers complete |
| PR | Issue linked, ≤4000 chars, AI usage tracked |
