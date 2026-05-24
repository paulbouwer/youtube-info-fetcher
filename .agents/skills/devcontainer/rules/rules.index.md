# Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the skill rules bundled under `$DEVCONTAINER_SKILL`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

The folder is flat (no category sub-folders); this index is therefore a file map.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Base image, container configuration, lifecycle hooks, modular script system, file structure | Authoring or reviewing any DevContainer configuration (always loaded) |
| [`features.md`](features.md) | DevContainer features versioning rules and excluded features | Authoring or reviewing the `features` block of `devcontainer.json` |
| [`extensions.md`](extensions.md) | Essential VS Code extensions and the language/format extension contribution model | Authoring or reviewing the extensions list |
| [`assembly.md`](assembly.md) | Composition, dedup, conflict resolution, and lifecycle script filename assignment across all loaded contributing rules | Running the Create or Review actions — they assemble the configuration end-to-end per this rule |
| [`security.md`](security.md) | Security requirements (non-root user, no privileged mode, mounts, secrets) and risk guidance | Authoring or reviewing for security posture |
| [`checklist.md`](checklist.md) | Consolidated single-pass compliance checklist | Running the Review action over an existing DevContainer |
