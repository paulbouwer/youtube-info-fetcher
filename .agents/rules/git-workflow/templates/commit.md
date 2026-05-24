# Git Commit Template

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

Template for commit messages following the Conventional Commits specification.

## Basic Template

```text
{type}({scope}): {description}

{body}

{trailers}
```

## Template With AI Contribution

Use this format when AI coding agents contributed to the changes:

```text
{type}({scope}): {description}

- {change-summary-1}
- {change-summary-2}

Co-authored-by: {Name} <{email}>
Co-authored-by: {Display Name} <{official email}>
```

**Note:** Human co-authors are optional. Include them when others contributed to the changes. Use the verified agent identities listed in `$AGENT_SYSTEM_FOLDER/rules/git-workflow/commit.md` § Agent Identities for the AI `Co-authored-by` trailer.

## Template Without AI Contribution

Use this format for human-only changes:

```text
{type}({scope}): {description}

- {change-summary-1}
- {change-summary-2}
```

## Field Definitions

| Field | Description | Example |
| ----- | ----------- | ------- |
| `{type}` | Change category | `feat`, `fix`, `docs` |
| `{scope}` | Affected area (optional) | `auth`, `api`, `parser` |
| `{description}` | Brief summary (≤50 chars) | `add user authentication` |
| `{body}` | Detailed explanation (optional) | Bullet points of changes |
| `{Name}` | Human co-author name | `Jane Doe` |
| `{email}` | Human co-author email | `jane.doe@example.com` |
| `{Display Name}` | AI agent display name from § Agent Identities | `GitHub Copilot`, `Claude Code` |
| `{official email}` | AI agent official email from § Agent Identities | `223556219+Copilot@users.noreply.github.com`, `claude@anthropic.com` |

## Examples

### Simple Feature (No AI)

```text
feat(auth): add JWT token validation
```

### Simple Fix (With AI)

```text
fix(parser): handle null input gracefully

Co-authored-by: GitHub Copilot <223556219+Copilot@users.noreply.github.com>
```

### Complex Feature (No AI)

```text
feat(integration): add HL7 message validation

- Implement schema-based validation for ORU messages
- Add custom error handling for malformed segments
- Support both v2.3 and v2.5 HL7 versions
```

### Complex Documentation (With AI)

```text
docs(prompts): streamline PR instructions

- Simplified workflow from 221 to 43 lines
- Added structured metadata headers
- Enhanced readability and reduced cognitive overhead

Co-authored-by: Claude Code <claude@anthropic.com>
```

### Multi-Scope Refactoring (With AI and Co-author)

```text
core(azure,messaging): update service bus configuration

- Migrate from legacy connection string format
- Add retry policies with exponential backoff
- Update environment-specific property files

Co-authored-by: Bob Smith <bob.smith@example.com>
Co-authored-by: GitHub Copilot <223556219+Copilot@users.noreply.github.com>
```

## Git Command Examples

### With Trailers (AI-Assisted, Signed)

```bash
git commit -S -m "feat(api): add user authentication endpoint

- Implement JWT token validation
- Add role-based access control" \
  --trailer "Co-authored-by: GitHub Copilot <223556219+Copilot@users.noreply.github.com>"
```

### Without Trailers (Human-Only, Signed)

```bash
git commit -S -m "fix(parser): correct message segment parsing"
```
