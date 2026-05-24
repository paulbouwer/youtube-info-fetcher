# Pull Request Template

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

Template for pull request descriptions.

## Template

```markdown
<!--
MANDATORY: The entire pull request description (including all text, spaces, newlines, emojis, and special characters) must not exceed 4000 characters.
-->

## 📝 Description

<!-- Provide a brief description of the changes in this PR -->

## 🔗 Related Issues

<!-- Link to any related issues or work items -->
<!-- Use closing keywords: Fixes #123, Closes #456, Resolves #789 -->

## 🚀 Changes

<!--
For each change, use the appropriate emoji and type:
🐛 fix | ✨ feat | 💥 breaking | 📚 docs | 🔧 config | 🧹 refactor | 🔒 security | ⚡ perf

Format each change using the What/Why pattern:
-->

**[emoji] [type](scope): Short description**
*What*: [Brief description of what was changed]
*Why*: [Brief description of why this change was made]
Files: `file1.ts` (`function1`, `function2`), `file2.ts` (config updates)

<!-- Add additional changes as needed -->

## 🙏 Additional Context

<!-- Add any other context about the PR here -->
<!-- Include screenshots for UI changes -->
<!-- Note any breaking changes or migration steps -->

## 🤖 Coding Agents Used

<!--
INCLUDE THIS SECTION ONLY IF:
1. AI agent/model trailers were found in git commits, AND
2. Including this section keeps the total description ≤ 4000 characters.

If the description would exceed 4000 characters, post this section as a PR comment instead.
-->

| Agent | Model | Commits |
| ----- | ----- | ------- |
| {agent-name} | {model-name} | {count} |
```

## Field Definitions

| Field | Description | Example |
| ----- | ----------- | ------- |
| `{agent-name}` | AI tool/interface (kebab-case) | `github-copilot`, `cursor` |
| `{model-name}` | AI model identifier | `claude-opus-4-5`, `gpt-4o` |
| `{count}` | Number of commits using this agent/model | `5` |

## Example: Filled Template

```markdown
# 📝 Description

Add JWT token validation for the authentication module.

## 🔗 Related Issues

Fixes #123

## 🚀 Changes

**✨ feat(auth): Add JWT validation middleware**
*What*: Implemented token validation for all protected routes
*Why*: Secure API endpoints against unauthorized access
Files: `src/middleware/auth.ts` (`validateToken`, `extractClaims`)

**🧹 refactor(auth): Extract token utilities**
*What*: Moved token parsing to dedicated utility module
*Why*: Improve testability and reusability
Files: `src/utils/token.ts` (`parseToken`, `isExpired`)

## 🙏 Additional Context

- Tokens expire after 1 hour
- Refresh token flow will be added in a follow-up PR

## 🤖 Coding Agents Used

| Agent | Model | Commits |
| ----- | ----- | ------- |
| github-copilot | gpt-4o | 3 |
```
