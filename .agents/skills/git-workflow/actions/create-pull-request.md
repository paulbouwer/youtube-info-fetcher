# Create Pull Request

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Create a rules-compliant pull request with issue linking and AI contribution tracking.

## Flow

### Prerequisites

This action assumes all bundled rules have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Gather all branch, commit, and issue context needed to construct the PR.

Load project rules:

- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/branch.md` — branch naming policy (for issue ID extraction)
- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/pull-request.md` — PR format policy
- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/templates/pull-request.md` — PR description template

Gather and cache:

- **Current branch** — `git branch --show-current`
- **Target branch** — detect default branch (default: `main`)
- **Issue ID** — extract from branch name per branch naming pattern
- **Issue context** — read issue title and description for PR content
- **Commits** — `git log {target}..{current} --oneline`
- **AI usage** — scan commit trailers for `agent` and `model` lines, aggregate by agent/model

Validate before proceeding:

- Branch must be pushed with upstream tracking
- Commits must exist between source and target
- **Work package `related:` linkage** — if a work package exists for this branch (search `$PROJECT_FOLDER/work/` for any `plan.md` whose Phases or Approach reference this issue, or whose folder was created on the branch), its `plan.md` MUST have a `related:` frontmatter value that is non-empty and not `TBD`. This enforces the Issue-First Development principle at the PR-creation boundary per `$WORK_TRACKING_SKILL/rules/core.md` § Issue-First Development and `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` § Draft Phase.

If validation fails, inform the user of the specific issue and how to resolve it. For a missing or `TBD` `related:` value, instruct the user to run the work-tracking skill's `update-work-package` action to set it before retrying PR creation.

### Step 2: Generate and Confirm

**Goal:** Build the PR title and description, then present for approval.

**Title** — Format per `$AGENT_SYSTEM_FOLDER/rules/git-workflow/pull-request.md`: derive type from branch prefix, scope and description from branch name or issue title. Title ≤72 characters.

**Description** — Use `$AGENT_SYSTEM_FOLDER/rules/git-workflow/templates/pull-request.md` to structure the description:

- Overview from issue context
- Related issue linked with closing keyword (e.g. `Fixes #{issue-id}`)
- Changes summary from commit list
- AI usage table from aggregated trailer data (if applicable)
- Total description ≤4000 characters

Present the complete PR preview (title, source branch, target branch, full description) for user approval before creating.

### Step 3: Create

**Goal:** Create the PR and confirm success.

Create the PR using the appropriate provider CLI with the approved title, description, and target branch.

Optionally assign reviewers or assignees if specified.

Confirm creation with the PR URL, number, source/target branches, and linked issue.

## Error Handling

| Error | Recovery |
| ----- | -------- |
| Branch not pushed | Inform user, instruct to push first |
| No commits to merge | Inform user, nothing to create |
| Issue not found | Proceed without issue context, warn user |
| Work package `related:` missing or `TBD` | Block PR creation; reference `$WORK_TRACKING_SKILL/actions/update-work-package.md` to set `related:` first |
| PR already exists | Display existing PR URL |
| Description exceeds limit | Trim content, consider moving AI usage to a comment |
| PR creation failed | Display error, check permissions |
