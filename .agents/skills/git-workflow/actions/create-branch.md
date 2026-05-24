# Create Branch

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Create a rules-compliant git branch linked to an issue, ensuring traceability and consistent naming.

## Flow

### Prerequisites

This action assumes all bundled rules have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Determine the issue, branch type, and description from available context.

Load project rules:

- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/branch.md` — branch naming policy and validation

Scan the conversation and repository state for:

- The issue or work item being addressed
- The type of change (feature, fix, docs, etc.)
- A suitable branch description

| Mode | When | Next Step |
|------|------|-----------|
| **Execute** | Issue ID, branch type, and description are clear from context | Go to Step 2 |
| **Discover** | Missing information — need to ask the user | Ask clarifying questions, then go to Step 2 |

If uncertain, default to **Discover**.

### Step 2: Construct and Validate

**Goal:** Build the branch name, validate it, and verify the source branch.

Construct the branch name per the naming pattern in `$AGENT_SYSTEM_FOLDER/rules/git-workflow/branch.md`. Validate against the branch naming regex.

Verify the source branch:

- Fetch latest from remote (`git fetch origin`)
- Confirm source branch exists (default: `main`)
- Check that the target branch name does not already exist locally or remotely

If the branch already exists, inform the user and offer to switch to the existing branch or choose a different name.

### Step 3: Create and Push

**Goal:** Create the branch, push to remote, and confirm.

Create the branch from the source and push with upstream tracking:

```bash
git checkout -b {branch-name} origin/{source-branch}
git push -u origin {branch-name}
```

Confirm creation with the branch name, source branch, tracking configuration, and linked issue.

## Error Handling

| Error | Recovery |
| ----- | -------- |
| Invalid branch type | Show valid types from branch policy, ask for correction |
| Source branch not found | List available branches, ask for selection |
| Branch already exists | Offer to switch or rename |
| Push failed | Check permissions and network connectivity |
| Invalid branch name | Show validation errors, ask for correction |
| Issue not found | Verify issue ID, check repository |
