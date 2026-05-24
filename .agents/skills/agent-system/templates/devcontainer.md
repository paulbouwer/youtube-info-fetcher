# <Domain> DevContainer Host Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

This rule declares how the DevContainer host satisfies the <domain> tool requirements catalogued in [`tooling.md`](tooling.md). It covers feature-based installs, runtime pins, and lifecycle hooks. Editor-side integration lives in [`vscode.md`](vscode.md).

<!-- Add: "This file follows the project's devcontainer lifecycle hooks catalog at <path>." when the project has one. -->

<!-- Runtimes precede tools so tool blocks can link to their declared runtime. -->

## Runtimes

<!-- Omit this entire section when every tool has Runtime: none (statically compiled binaries). -->

### `<Runtime>`

| Field   | Value                                              |
| ------- | -------------------------------------------------- |
| Version | <`1.24`, `22`, …> (MUST pin to ≥ major.minor)      |
| Feature | `<feature URI>`                                    |

```jsonc
{
  "features": {
    "<feature URI>": { "version": "<pin>" }
  }
}
```

## Tools

### `<tool-name>`

| Field          | Value                                                                |
| -------------- | -------------------------------------------------------------------- |
| Install        | <`Feature` / `Lifecycle script` / `Base image`>                      |
| Runtime        | [`<runtime>`](#<runtime>) (link to Runtime block above) or `none`    |
| Lifecycle hook | <`none`, `<script-name> (post-create)`, …>                           |
| Config file    | [`<config>`](tooling.md#<tool-name>) (link to tooling.md anchor)     |

```jsonc
{
  "features": {
    "<feature URI>": { "version": "<pin>" }
  }
}
```

<!-- Optional Notes paragraph for clarifications that don't fit a row. -->
