# DevContainer Extensions Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

Requirements for VS Code extensions in DevContainer configurations.

## Essential Extensions

All DevContainers **MUST** include these minimum required extensions, organised into logical bundles with documentation comments:

```jsonc
{
  "customizations": {
    "vscode": {
      "extensions": [
        // Coding agents
        "GitHub.copilot-chat",

        // Formatting
        "esbenp.prettier-vscode",

        // Spell checking
        "streetsidesoftware.code-spell-checker",
        "streetsidesoftware.code-spell-checker-australian-english",
      ],
    },
  },
}
```

> **Note:** `GitHub.copilot` is intentionally omitted — it is auto-installed as a dependency of `GitHub.copilot-chat`.

> **Note:** Extensions cannot be externalised to `.vscode/extensions.json` for auto-install at this time. DevContainer extensions must be declared in `devcontainer.json`.

> **Note:** Markdown is an essential format — every project authors markdown. The devcontainer skill **MUST** always load `rules/formats/markdown/development-environment.md` together with its sibling per-environment files (`devcontainer.md`, `vscode.md`) to pull in the markdown extensions. These extensions are not listed above because they are managed by the format rule, not the essential extensions list.

## Language and Format Extensions

Language-specific extensions are defined in `rules/languages/<language>/development-environment.md` (or, under the split shape, in the sibling `vscode.md`). Format-specific extensions are defined in `rules/formats/<format>/development-environment.md` (or, under the split shape, in the sibling `vscode.md`). When creating or reviewing a DevContainer, add the extensions for each included language and format.

## Extension Guidelines

### Review Guidelines

When reviewing existing DevContainers:

| Action     | When                                                                          |
| ---------- | ----------------------------------------------------------------------------- |
| **Add**    | Essential extensions are missing                                              |
| **Keep**   | Project-relevant extensions that benefit the team                             |
| **Remove** | Only if extension is harmful, deprecated, or creates security vulnerabilities |

**Important**: Do NOT aggressively remove existing extensions. The essential extensions list is a **minimum requirement**, not a maximum.

### Creation Guidelines

When creating a new DevContainer, use ONLY:

- Essential extensions (listed above)
- Language-specific extensions from language rules
- Format-specific extensions from format rules (markdown always included)

Personal tools belong in developer's VS Code user `settings.json`, not shared DevContainer configurations. Work with the developer to configure their user `settings.json` with the tools that are not part of the rules relevant to this project or repository.

### Bundling and Documentation

Extensions **MUST** be:

1. **Grouped into logical bundles** under descriptive headers
2. **Documented with comments** explaining purpose

#### Bundle Header Format

Use single-line comments to separate extension bundles:

```jsonc
// Bundle Name (e.g., "Python Development", "Database Tools")
```

#### Extension Comments

Per-extension description comments are **not required**. Bundle headers provide sufficient context. Only add inline comments when an extension's purpose is genuinely unclear from its identifier.
