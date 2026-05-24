# Branch Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

Project-specific branch naming configuration. Defines the allowed types, validation rules, and enforcement hooks for this project.

## Branch Types

Branch types align with commit types. See [Commit Rule](./commit.md#commit-types) for descriptions.

| Type | Purpose |
| ---- | ------- |
| `feat` | New feature or capability |
| `fix` | Bug fix or defect resolution |
| `core` | Infrastructure or tooling changes |
| `docs` | Documentation updates |
| `refactor` | Code restructuring without behavior change |
| `test` | Test additions or modifications |

## Validation Pattern

```regex
^(feat|fix|core|docs|refactor|test)/[0-9]+-[a-z0-9][a-z0-9-]*$
```

## Examples

### Valid Branch Names

```text
core/8899-setup-devcontainer
feat/8911-integration-specification-definition
fix/9001-null-pointer-validation
docs/9120-update-api-documentation
refactor/8945-extract-parser-logic
test/9050-add-integration-tests
```

### Invalid Branch Names

| Example | Issue |
| ------- | ----- |
| `feature/add-new-api` | Wrong type (`feature` vs `feat`), missing issue ID |
| `feat/Add-New-API` | Capitalization not allowed |
| `feat/8911_new_feature` | Underscores not allowed in description |
| `fix/8911` | Missing description |
| `8911-new-feature` | Missing type prefix |

## Common Questions

### Can I use `feature/` instead of `feat/`?

No. The validation requires exact type matching: `feat`, `fix`, `core`, `docs`, `refactor`, or `test`.

### What if I forget the issue ID?

Create the issue first, then create your branch with the ID. All work should be traceable to an issue.

### Can I use uppercase in branch names?

No. Branch names must be lowercase to ensure consistency and avoid case-sensitivity issues across different operating systems.

### How do I fix an incorrectly named branch?

- **Option 1: Rename the branch**

```bash
git branch -m old-branch-name feat/9121-correct-name
git push origin feat/9121-correct-name
git push origin --delete old-branch-name
```

- **Option 2: Create new branch from existing commits**

```bash
git checkout -b feat/9121-correct-name
git push origin feat/9121-correct-name
# Delete old branch via UI or command line
```

## Git Hooks

### Pre-Push Hook

A `pre-push` hook validates branch naming conventions before pushing to remote.

**Validation:**

- Branch name matches pattern: `^(feat|fix|core|docs|refactor|test)/[0-9]+-[a-z0-9][a-z0-9-]*$`
- Prevents pushing branches with invalid names

**Installation:** Run the git hooks installation script after cloning the repository.

## References

- [Conventional Branch](https://conventional-branch.github.io/): Branch naming inspiration
- [Commit Rule](./commit.md): Commit message formatting
