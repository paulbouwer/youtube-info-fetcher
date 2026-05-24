# Go Tooling Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

- This rule is **prescriptive**: every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide.
- A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.

The Go toolchain itself (`go`, `go mod`, `go test`, `go build`, `gofmt`) is a runtime, not a tool catalogued here; its presence is implied by the Go runtime declaration in the host environment. Conventions for invoking these built-in subcommands live in `core.md`, `testing.md`, and the project's task runner.

## Tool Catalog

| Type                  | Tool             | Purpose                                                     |
| --------------------- | ---------------- | ----------------------------------------------------------- |
| Linter & formatter    | `golangci-lint`  | Aggregated linter and formatter runner for Go source code   |
| Vulnerability scanner | `govulncheck`    | Scan Go modules and binaries for known vulnerabilities      |

## `golangci-lint`

| Field       | Value                                  |
| ----------- | -------------------------------------- |
| Purpose     | Run an aggregated suite of Go linters and formatters against the module under a single configuration, covering correctness, style, security, and import grouping. |
| Runtime     | none                                   |
| Config file | `.golangci.yml` at the repository root |

The recommended configuration for `golangci-lint` is:

```yaml
version: "2"

linters:
  default: standard
  enable:
    - bodyclose
    - gosec
    - misspell
    - revive
  settings:
    govet:
      enable-all: true
    revive:
      rules:
        - name: exported
          arguments:
            - "checkPrivateReceivers"

formatters:
  enable:
    - gofmt
    - goimports
  settings:
    goimports:
      local-prefixes: github.com/<org>/<repo>
```

| Setting                                | Value                       | Rationale                                                                                                                                                                         |
| -------------------------------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `version`                              | `"2"`                       | Pin the configuration schema to the v2 format. Older v1 configs MUST be migrated with `golangci-lint migrate`.                                                                    |
| `linters.default`                      | `standard`                  | Enable the curated standard linter set (`errcheck`, `govet`, `staticcheck`, `unused`, `gosimple`, `ineffassign`, `typecheck`) as the baseline before opting in to additional linters. |
| `linters.enable.bodyclose`             | enabled                     | Detect HTTP response bodies that are not closed, a common resource-leak source in Go services.                                                                                     |
| `linters.enable.gosec`                 | enabled                     | Surface common security issues (weak crypto, command injection, unsafe usage) statically rather than relying on runtime discovery.                                                 |
| `linters.enable.misspell`              | enabled                     | Catch misspellings in comments, strings, and identifiers so user-facing text and API names stay clean.                                                                             |
| `linters.enable.revive`                | enabled                     | Run an extensible style linter complementing the standard set, particularly to enforce documentation on exported identifiers.                                                      |
| `linters.settings.govet.enable-all`    | `true`                      | Run every available `go vet` analyser (shadow, fieldalignment, etc.) rather than the default subset, surfacing more correctness issues.                                            |
| `linters.settings.revive.rules`        | `exported / checkPrivateReceivers` | Require documentation comments on exported identifiers, including methods on private receivers reachable through exported interfaces.                                       |
| `formatters.enable.gofmt`              | enabled                     | Delegate canonical Go formatting to the built-in `gofmt`, invoked through golangci-lint so a single command formats and lints together.                                            |
| `formatters.enable.goimports`          | enabled                     | Manage import grouping and unused-import removal alongside formatting, so imports stay organised on every run.                                                                     |
| `formatters.settings.goimports.local-prefixes` | `github.com/<org>/<repo>` | Group imports rooted at the project's module path into their own block, distinct from third-party imports. Each project MUST replace the placeholder with its own module path. |

`gofmt` and `goimports` are built-in Go toolchain binaries; the `formatters` block above delegates to them rather than treating them as separate tools, so a single configuration file governs both linting and formatting and the formatter cannot produce output the linter rejects.

When a `golangci-lint` rule does not apply, suppress it inline with a `//nolint:<linter>` directive on the offending line and a comment explaining why the suppression is safe. Suppression MUST be as narrow as possible (single line, not whole file or whole package) and MUST be justified.

Every installation of `golangci-lint` (CLI, editor integrations, future agent integrations) MUST resolve `.golangci.yml` at the repository root, so linting and formatting stay consistent regardless of where they run.

## `govulncheck`

| Field       | Value        |
| ----------- | ------------ |
| Purpose     | Scan the module's source and dependency tree for known vulnerabilities published in the Go vulnerability database, narrowing reports to call paths actually reachable from the project. |
| Runtime     | Go ≥ 1.24    |
| Config file | none         |

`govulncheck` uses its built-in defaults; no project configuration is required.

`govulncheck` is distributed only as a Go module (no upstream binary releases); installation requires the Go toolchain. Each environment MUST provide a `govulncheck` binary on `PATH` whose version is pinned, so vulnerability scans are reproducible across CLI, CI, and agent invocations.
