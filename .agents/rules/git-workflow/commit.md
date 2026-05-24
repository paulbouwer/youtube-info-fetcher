# Commit Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

Project-specific commit configuration. Defines the allowed types, AI attribution tracking, signing policy, and validation hooks for this project.

## Commit Types

| Type | Purpose | Example Use Case |
| ---- | ------- | ---------------- |
| `feat` | New feature or capability | Adding new API endpoint, implementing new workflow |
| `fix` | Bug fix or defect resolution | Fixing null pointer exception, correcting validation logic |
| `core` | Infrastructure or tooling changes | DevContainer setup, CI/CD pipeline updates |
| `docs` | Documentation updates | README changes, API documentation, code comments |
| `refactor` | Code restructuring without behavior change | Extracting methods, reorganizing modules |
| `test` | Test additions or modifications | Adding unit tests, updating test fixtures |

## Scope Guidelines

Scope provides additional context about the area affected:

```text
feat(auth): add token validation middleware
fix(parser): handle null pointer in message parser
core(azure): update service bus configuration
docs(api): document REST endpoint parameters
```

- Use lowercase, alphanumeric characters with dashes/underscores/dots
- Keep concise and meaningful

## AI Attribution Tracking

When AI coding agents contribute to a commit, include a `Co-authored-by` trailer that references the agent's official identity.

### Trailer Format

```text
Co-authored-by: {Display Name} <{official email}>
```

All `Co-authored-by` trailers MUST appear in the commit message footer, separated from the body by a single blank line.

### Agent Identities

| Agent | Co-authored-by Trailer |
| ----- | ---------------------- |
| GitHub Copilot | `Co-authored-by: GitHub Copilot <223556219+Copilot@users.noreply.github.com>` |
| Claude Code | `Co-authored-by: Claude Code <claude@anthropic.com>` |

### Other Agents

For AI agents not listed above, do not invent placeholder addresses. Attribution for those agents is captured through a separate tracing mechanism (to be defined in a future session).

## Signed Commits

All commits MUST be signed.

See `$GIT_WORKFLOW_SKILL/rules/commit.md` § Signing for SSH signing configuration.

## Validation Pattern

```regex
^(feat|fix|core|docs|refactor|test)(\([a-z0-9._-]+\))?: .{1,72}$
```

## Body Style

When the commit needs a body, follow these rules:

- **One blank line** between the subject and the body — never more.
- **Prefer a tight bullet list** to summarise the change. Bullets MUST be contiguous (no blank lines between them).
- **One blank line** between distinct paragraphs or between a bullet list and trailers.
- Wrap prose at ~72 characters; bullets MAY exceed when needed for clarity.

### Authoring at the CLI

`git commit -m` treats **each `-m` flag as a separate paragraph** and inserts a blank line between them. This breaks tight bullet lists.

- **DO** put the entire body (including all bullets) inside a **single** `-m` argument with embedded newlines, or use `-F <file>` / `-F -` for multi-line bodies.
- **DO NOT** use one `-m` per bullet — this produces blank lines between bullets.

```bash
# Good — single -m, tight bullets
git commit -S -m "docs: add initial README and MIT license

- Add project README with overview and status
- Add MIT LICENSE file"

# Bad — separate -m per bullet produces blank lines between them
git commit -S -m "docs: add initial README and MIT license" \
              -m "- Add project README with overview and status" \
              -m "- Add MIT LICENSE file"
```

## Git Hooks

### Commit-msg Hook

A `commit-msg` hook validates Conventional Commit format and trailer consistency.

**Installation:** Run the git hooks installation script after cloning the repository.

## References

- [Conventional Commits Specification](https://www.conventionalcommits.org/)
- [Branch Policy](./branch.md)
- [Pull Request Policy](./pull-request.md)
