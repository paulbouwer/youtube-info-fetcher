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

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|
| Create Branch | `$GIT_WORKFLOW_SKILL/actions/create-branch.md` | Create a rules-compliant branch linked to an issue | User wants to create a new git branch |
| Commit | `$GIT_WORKFLOW_SKILL/actions/commit.md` | Create conventional commits with AI attribution | User wants to commit staged or unstaged changes |
| Create Pull Request | `$GIT_WORKFLOW_SKILL/actions/create-pull-request.md` | Generate a rules-compliant PR linked to an issue | User wants to open or submit a pull request |

### Rules

| Index | File |
|-------|------|
| Skill Rules Index | `$GIT_WORKFLOW_SKILL/rules/rules.index.md` |
| Project Rules Index | `$AGENT_SYSTEM_FOLDER/rules/git-workflow/rules.index.md` |

Consult each index and load only the rule files whose **Load when** matches the selected capability.

## Flow

### Prerequisites

- [ ] Detect repo platform provider (eg. GitHub, Azure DevOps) from `git remote` URL
- [ ] Verify repository access and authentication

### Execution Steps

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. Load skill and project rules via the indexes listed in the **Rules** section — only the rules whose **Load when** matches the selected capability
3. Execute the selected capability
