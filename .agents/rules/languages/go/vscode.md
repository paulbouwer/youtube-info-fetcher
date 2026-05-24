# Go VS Code Workbench Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

VS Code is a workbench that runs inside a host environment and provides an editor surface for productive Go development.

This rule is **prescriptive**. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

Two prescriptive levels apply:

- Extensions in [`## Extensions Satisfying Tool Requirements`](#extensions-satisfying-tool-requirements) MUST be installed. Each pairs with a [`tooling.md`](tooling.md) entry and provides editor parity for the underlying CLI tool.
- Extensions in `## Authoring Aids` SHOULD be installed when present. No authoring-aid extensions are declared in this pass; the section is intentionally omitted.

## Extensions Satisfying Tool Requirements

### `golang.go`

| Field             | Value                                       |
| ----------------- | ------------------------------------------- |
| Extension ID      | `golang.go`                                 |
| Settings          | see snippet below                           |
| Reads config from | [`.golangci.yml`](tooling.md#golangci-lint) |

```jsonc
{
  "[go]": {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "golang.go",
    "editor.codeActionsOnSave": {
      "source.organizeImports": "explicit",
    },
  },
  "go.lintTool": "golangci-lint",
  "go.testFlags": ["-race"],
  "go.coverOnSingleTest": true,
}
```

| Setting                                            | Value             | Rationale                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[go].editor.formatOnSave`                         | `true`            | Trigger the language server to format Go files on save so authored content stays canonical without an explicit CLI invocation.                                       |
| `[go].editor.defaultFormatter`                     | `golang.go`       | Disambiguate the formatter when other formatter extensions are installed, so the Go extension's `gofmt`/`goimports` pipeline is the one that runs on save.           |
| `[go].editor.codeActionsOnSave.source.organizeImports` | `"explicit"` | Run `goimports`-style import organisation on save, keeping import groups (including the project-local prefix block) consistent with the formatter's CLI behaviour.   |
| `go.lintTool`                                      | `"golangci-lint"` | Direct the editor's lint integration at `golangci-lint` rather than the extension's historical default (`staticcheck`), so editor diagnostics match CLI/CI exactly.  |
| `go.testFlags`                                     | `["-race"]`       | Enable the race detector for tests run from the editor's test UI, matching the project's CI invocation.                                                              |
| `go.coverOnSingleTest`                             | `true`            | Show inline coverage gutters when running an individual test from the editor, providing immediate feedback without enabling project-wide coverage on every save.     |

The `golang.go` extension also bundles `gopls` (the Go language server, providing go-to-definition, hover, completion, references, and diagnostics), the Delve debugger UI, and a test-runner UI. These are installed by the extension on first use rather than provisioned by the host, and require no separate declaration here.

The extension reads `.golangci.yml` indirectly: the editor invokes `golangci-lint` (provided by the host) which resolves the config file at the repository root, so editor diagnostics mirror CLI output exactly.
