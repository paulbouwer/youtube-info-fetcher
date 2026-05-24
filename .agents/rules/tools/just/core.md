# Just — Core Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

[just](https://github.com/casey/just) is the project command runner. It replaces `make` for task orchestration — it is **not** a build system. Go's toolchain handles compilation and dependency resolution; `just` provides a consistent interface to invoke those tools.

## Principles

1. **Discoverability** — `just` (no arguments) lists all available recipes
2. **Consistency** — Use canonical recipe names across all projects
3. **Safety** — Recipes run under `bash -euo pipefail` strict mode
4. **Cross-platform** — Use built-in `os()` and `arch()` for platform-specific behaviour

## Configuration

### Justfile Header

Every `justfile` **MUST** start with these settings:

```just
set shell := ["bash", "-euo", "pipefail", "-c"]
set dotenv-load

# List available recipes
default:
    @just --list
```

| Setting           | Purpose                                            |
| ----------------- | -------------------------------------------------- |
| `set shell`       | Enforces bash strict mode for all recipes          |
| `set dotenv-load` | Loads `.env` file automatically when present       |
| `default` recipe  | Running `just` with no arguments lists all recipes |

### Environment Files

Use `.env` for local configuration. Add `.env` to `.gitignore` and provide a `.env.example` template:

```bash
# .env.example — copy to .env and fill in values
API_KEY=
LOG_LEVEL=info
```

## Canonical Recipes

All projects **MUST** provide these standard recipe names where applicable:

| Recipe     | Group   | Purpose                               |
| ---------- | ------- | ------------------------------------- |
| `build`    | build   | Compile the project                   |
| `run`      | build   | Build and run the project             |
| `test`     | test    | Run unit tests                        |
| `test-all` | test    | Run all tests including integration   |
| `lint`     | quality | Run linters                           |
| `fmt`      | quality | Format source code                    |
| `check`    | quality | Run fmt + lint + test (CI-equivalent) |
| `clean`    | build   | Remove build artefacts                |
| `tidy`     | build   | Clean up dependencies                 |

Additional recipes may be added as needed. Use descriptive names and group them logically.

## Recipe Structure

### Groups

Use the `[group]` attribute and doc comments for discoverability:

```just
[group: 'build']
# Compile the project
build:
    go build -o bin/ ./cmd/...

[group: 'test']
# Run unit tests with race detection
test:
    go test -race ./...

[group: 'quality']
# Run linter
lint:
    golangci-lint run ./...

[group: 'quality']
# Format source code
fmt:
    gofmt -w .
    goimports -w .

[group: 'quality']
# Run fmt, lint, and test (CI-equivalent)
check: fmt lint test
```

### Recipe Arguments

Use arguments for configurable recipes:

```just
[group: 'build']
# Build for a specific OS and architecture
build-for os arch:
    GOOS={{os}} GOARCH={{arch}} go build -o bin/app-{{os}}-{{arch}} ./cmd/...

[group: 'test']
# Run tests matching a pattern
test-match pattern:
    go test -race -run '{{pattern}}' ./...
```

### Dependencies

Use recipe dependencies for composition:

```just
[group: 'quality']
# Run all quality checks
check: fmt lint test
```

## Cross-Platform Recipes

Use `just`'s built-in functions for platform-aware recipes:

```just
# Binary extension based on OS
bin_ext := if os() == "windows" { ".exe" } else { "" }

# Build output name
app_name := "myapp" + bin_ext

[group: 'build']
# Build for the current platform
build:
    go build -o bin/{{app_name}} ./cmd/...

[group: 'build']
# Build for all target platforms
build-all:
    GOOS=linux   GOARCH=amd64 go build -o bin/myapp-linux-amd64 ./cmd/...
    GOOS=linux   GOARCH=arm64 go build -o bin/myapp-linux-arm64 ./cmd/...
    GOOS=darwin  GOARCH=amd64 go build -o bin/myapp-darwin-amd64 ./cmd/...
    GOOS=darwin  GOARCH=arm64 go build -o bin/myapp-darwin-arm64 ./cmd/...
    GOOS=windows GOARCH=amd64 go build -o bin/myapp-windows-amd64.exe ./cmd/...
```

### Available Platform Functions

| Function      | Returns          | Example Values              |
| ------------- | ---------------- | --------------------------- |
| `os()`        | Operating system | `linux`, `macos`, `windows` |
| `os_family()` | OS family        | `unix`, `windows`           |
| `arch()`      | CPU architecture | `x86_64`, `aarch64`         |

## Naming Conventions

| Element      | Convention                          | Example                      |
| ------------ | ----------------------------------- | ---------------------------- |
| Recipe names | kebab-case                          | `build-all`, `test-match`    |
| Variables    | snake_case                          | `bin_ext`, `app_name`        |
| Groups       | lowercase single word               | `build`, `test`, `quality`   |
| Arguments    | lowercase single word or snake_case | `os`, `arch`, `test_pattern` |

## Patterns to Avoid

| Pattern                      | Problem                                    | Alternative                                        |
| ---------------------------- | ------------------------------------------ | -------------------------------------------------- |
| Recipes without doc comments | Not discoverable in `just --list`          | Always add `# comment` above recipe                |
| Ungrouped recipes            | Cluttered `--list` output as project grows | Use `[group]` attribute                            |
| Complex logic in recipes     | Hard to read and debug                     | Extract to a bash script, call from recipe         |
| Hardcoded paths              | Breaks across platforms                    | Use variables and `just` built-in functions        |
| `.PHONY` / make idioms       | Not applicable to `just`                   | `just` recipes are always re-run (no file targets) |

## Reference Material

- [Just Programmer's Manual](https://just.systems/man/en/)
- [casey/just on GitHub](https://github.com/casey/just)
