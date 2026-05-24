# Commit

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Create conventional commits with proper grouping and AI attribution tracking.

## Flow

### Prerequisites

This action assumes all bundled rules have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Understand the current state of changes and determine the commit strategy.

Load project rules:

- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/commit.md` — commit types, AI trailer configuration, signing policy
- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/templates/commit.md` — commit message templates

Check `git status` to identify staged and unstaged changes.

| Condition | Strategy |
| --------- | -------- |
| Files are pre-staged | Single commit flow — proceed to Step 3 with the staged files |
| No files are staged | Analyse and group — proceed to Step 2 |

### Step 2: Analyse and Group

**Goal:** Analyse unstaged changes and propose logical, atomic commit groups.

Review all changes with `git diff` and group into logical commits per `$GIT_WORKFLOW_SKILL/rules/commit.md` § Commit Grouping:

| Group Together | Do Not Group |
| -------------- | ------------ |
| Same feature/concern | Different features |
| Same change type | Docs with unrelated code |
| Logical dependencies | Config with unrelated features |
| Same scope/module | Refactoring with new features |

If an issue is available from the current branch name, read its details for grouping context.

Present the proposed commit plan showing each group with its type, scope, description, and files. Wait for user confirmation before proceeding.

### Step 3: Stage and Commit

**Goal:** Stage files, generate commit messages, and execute each commit.

For each commit group (or the single pre-staged set):

1. **Stage** the files for this group (`git add <files>`). Never use `git add .` for multi-commit workflows.
2. **Verify** staged changes with `git diff --cached` to confirm the right files are included.
3. **Generate** the commit message per `$AGENT_SYSTEM_FOLDER/rules/git-workflow/templates/commit.md` — subject ≤50 characters, imperative mood, body lines ≤72 characters.
4. **Determine attribution** — check whether changes were made with AI assistance. If so, include all required trailers (`Co-authored-by`, `agent`, `model`) per `$GIT_WORKFLOW_SKILL/rules/commit.md` § AI Attribution and `$AGENT_SYSTEM_FOLDER/rules/git-workflow/commit.md`. All trailers must be present together — partial sets are invalid.
5. **Check signing** — if signing is required (per `$AGENT_SYSTEM_FOLDER/rules/git-workflow/commit.md`), verify signing is configured (`git config --get user.signingkey`). If not configured, inform the user and reference `$GIT_WORKFLOW_SKILL/rules/commit.md` § Signing. Use the `-S` flag when signing is required.
6. **Execute** the commit with the appropriate command and trailers.

Repeat for remaining groups if this is a multi-commit workflow.

### Step 4: Verify

**Goal:** Confirm all commits were created correctly.

Run `git log --oneline -n {count}` to verify all commits appear with correct conventional format and trailers.

## Error Handling

| Error | Recovery |
| ----- | -------- |
| No changes detected | Inform user, exit workflow |
| Signing not configured | Reference `$GIT_WORKFLOW_SKILL/rules/commit.md` § Signing, block commit |
| Signing failed | Inform user, suggest troubleshooting |
| Commit hook rejected | Display validation error, allow correction |
| Staging failed | Check file paths and permissions |
