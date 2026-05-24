# Go DevContainer Host Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

DevContainer is a host environment that owns the filesystem, runtimes, and CLI tools available for productive Go development.

This rule is **prescriptive**: every runtime and tool listed below is a REQUIREMENT this host MUST provide. Tool requirements are catalogued in [`tooling.md`](tooling.md); the cross-environment satisfaction map is in [`development-environment.md`](development-environment.md).

Runtimes are declared first because tools below resolve their `Runtime` rows against this block. Lifecycle hook references in tool blocks resolve to named scripts in the project's devcontainer lifecycle hooks catalog or to `none`.

## Runtimes

### `Go`

| Field   | Value                                  |
| ------- | -------------------------------------- |
| Version | `1.24`                                 |
| Feature | `ghcr.io/devcontainers/features/go:1`  |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers/features/go:1": {
      "version": "1.24",
    },
  },
}
```

The Go feature provisions the Go toolchain (`go`, `go mod`, `go test`, `go build`, `gofmt`), sets `GOPATH=/go`, and adds `/go/bin` to `PATH`. Binaries installed via `go install` from a lifecycle hook are therefore available on `PATH` without further configuration.

## Tools

### `golangci-lint`

| Field          | Value                                                  |
| -------------- | ------------------------------------------------------ |
| Install        | `ghcr.io/devcontainers-extra/features/golangci-lint:1` |
| Runtime        | none                                                   |
| Lifecycle hook | none                                                   |
| Config file    | [`.golangci.yml`](tooling.md#golangci-lint)            |

```jsonc
{
  "features": {
    "ghcr.io/devcontainers-extra/features/golangci-lint:1": {
      "version": "2.11.4",
    },
  },
}
```

### `govulncheck`

| Field          | Value                                          |
| -------------- | ---------------------------------------------- |
| Install        | lifecycle hook (`go install`)                  |
| Runtime        | [`Go`](#go)                                    |
| Lifecycle hook | `20-go-tools-install.sh` (post-create)         |
| Config file    | [none](tooling.md#govulncheck)                 |

`govulncheck` has no upstream binary releases and no trusted devcontainer feature; the Go toolchain installs it from source via the `20-go-tools-install.sh` post-create hook. The hook invokes `go install golang.org/x/vuln/cmd/govulncheck@vX.Y.Z` at a pinned module version (never `@latest`) so the `govulncheck` binary on `PATH` is reproducible across environments. The pinned version SHOULD remain aligned with any minimum-runtime constraint declared in [`tooling.md`](tooling.md#govulncheck), and the hook is the home for any future Go-toolchain-distributed binary the host MUST provide.
