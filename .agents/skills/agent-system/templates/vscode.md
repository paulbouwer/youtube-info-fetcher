# <Domain> VS Code Workbench Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

This rule declares how the VS Code workbench satisfies the <domain> tool requirements catalogued in [`tooling.md`](tooling.md), and lists optional authoring aids that improve the editing experience without corresponding to a `tooling.md` requirement.

The two H2 sections carry different prescriptive levels:

- **Extensions Satisfying Tool Requirements** — extensions in this section MUST be installed; each pairs with a [`tooling.md`](tooling.md) entry.
- **Authoring Aids** — extensions in this section SHOULD be installed; they have no [`tooling.md`](tooling.md) counterpart and do not satisfy a tool requirement.

## Extensions Satisfying Tool Requirements

### `<tool-name>`

| Field             | Value                                                            |
| ----------------- | ---------------------------------------------------------------- |
| Extension ID      | `<publisher.extension>`                                          |
| Settings          | <`none` or "see snippet below">                                  |
| Reads config from | [`<config>`](tooling.md#<tool-name>) (link to tooling.md anchor) |

```jsonc
{
  "<setting>": "<value>"
}
```

<!--
Settings minimalism: include ONLY settings that materially differ from extension defaults.
- Bridge settings (editor.defaultFormatter, editor.formatOnSave, language-scoped editor-typing) are valid.
- Restating an extension default (e.g. shellcheck.enable: true when true is default) is non-conformant.
- Set Settings: none and omit the snippet when no overrides are needed.
-->

## Authoring Aids

<!-- Omit this section if no authoring aids are recommended. -->

### `<extension-name>`

| Field             | Value                       |
| ----------------- | --------------------------- |
| Extension ID      | `<publisher.extension>`     |
| Settings          | <`none` or "see snippet">   |
| Reads config from | `N/A`                       |
