---
name: git-workflow
description: Execute git workflow operations including branch creation, commits, and pull requests
---

# Git Workflow Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

This skill provides capabilities for executing git workflow operations that comply with organisational rules. It covers the complete development lifecycle from branch creation through pull request submission.

## Configuration

### Capabilities

| Capability | Action | Description |
|------------|--------|-------------|
| Create Branch | `$GIT_WORKFLOW_SKILL/actions/create-branch.md` | Create a rules-compliant branch linked to an issue |
| Commit | `$GIT_WORKFLOW_SKILL/actions/commit.md` | Create conventional commits with AI attribution |
| Create Pull Request | `$GIT_WORKFLOW_SKILL/actions/create-pull-request.md` | Generate a rules-compliant PR linked to an issue |

### Bundled Rules

| Rule | File | Description |
|----------|------|-------------|
| Rules Index | `$GIT_WORKFLOW_SKILL/rules/rules.index.md` | Lazy-loading index for this skill's rules |
| Core | `$GIT_WORKFLOW_SKILL/rules/core.md` | Cross-cutting workflow principles: issue-first development, conventional commits, atomic changes, AI attribution, protected branches, provider detection |
| Branch | `$GIT_WORKFLOW_SKILL/rules/branch.md` | Branch structure, lifecycle, and project policy reference |
| Commit | `$GIT_WORKFLOW_SKILL/rules/commit.md` | Commit message structure, grouping, AI attribution mechanics, signing configuration |
| Pull Request | `$GIT_WORKFLOW_SKILL/rules/pull-request.md` | PR structure, content requirements, issue linking, pre-PR validation |
| Checklist | `$GIT_WORKFLOW_SKILL/rules/checklist.md` | Validation checklist for all workflow operations |

Actions load additional project rules from `$AGENT_SYSTEM_FOLDER/rules/git-workflow/` based on the capability being executed (branch policy, commit policy, pull request policy).

## Flow

### Prerequisites

- [ ] Detect repo platform provider (eg. GitHub, Azure DevOps) from `git remote` URL
- [ ] Verify repository access and authentication

### Execution Steps

1. Load the bundled rules listed in the **Bundled Rules** section into context
2. Determine the user's intent and select the matching capability from the **Capabilities** section
3. Execute the selected capability — bundled rules are already in context; the action loads relevant project rules from `$AGENT_SYSTEM_FOLDER/rules/git-workflow/`
