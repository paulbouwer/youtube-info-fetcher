# Commit Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$GIT_WORKFLOW_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Structural rules for commit messages, change grouping, and AI attribution. These define the commit workflow mechanics that ensure consistency and traceability.

## Conventional Commits Format

All commits follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```text
{type}[optional scope]: {description}

[optional body]

[optional footer(s)]
```

### Message Structure

| Element | Requirement |
| ------- | ----------- |
| Type | REQUIRED — must be a valid type from project commit policy |
| Scope | OPTIONAL — area affected |
| Description | REQUIRED — imperative mood, ≤50 characters |
| Body | OPTIONAL — explain what and why, ≤72 chars/line |
| Trailers | CONDITIONAL — required per project AI attribution policy |

### Description Rules

- Maximum 50 characters (subject line limit)
- Use imperative mood ("add" not "added" or "adds")
- No period at the end
- Start with lowercase after the colon

### Body Rules

- Maximum 72 characters per line
- Explain the what and why, not the how
- Separate from subject with a blank line

## AI Attribution

When AI coding agents contribute to a commit, their contribution must be attributed using git trailers. The `Co-authored-by` trailer is the standard git convention for attribution:

```text
Co-authored-by: {Agent Name} <{model-name}@{agent-name}>
```

Additional project-specific trailers (agent, model) and their accepted values are defined in the project commit policy.

## Commit Grouping

### Group Together

- Related changes serving a single purpose
- Same feature, bug, or task
- Logically dependent changes (test + implementation)

### Separate

- Different features or bugs
- Unrelated documentation
- Independent refactoring

## Signing

SSH is the preferred signing format for its simplicity compared to GPG. Key configuration:

```bash
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519
git config --global commit.gpgsign true
```

For local signature verification, configure an allowed signers file:

```bash
echo "your_email@example.com $(cat ~/.ssh/id_ed25519.pub)" >> ~/.ssh/allowed_signers
git config --global gpg.ssh.allowedSignersFile ~/.ssh/allowed_signers
```

Verification commands:

```bash
git config --get user.signingkey      # Verify signing key is set
git config --get commit.gpgsign       # Verify signing is enabled
git log --show-signature -1           # Verify last commit signature
```

Whether signing is required is defined in the project commit policy.

## Project Commit Policy

Project-specific commit configuration is defined in `$AGENT_SYSTEM_FOLDER/rules/git-workflow/commit.md`. This includes:

- Allowed commit types and their purposes
- Scope guidelines and examples
- AI attribution trailer configuration (agent and model values)
- Signing enforcement policy
- Validation regex and git hooks
