# Go — Core Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

Go is the primary language for this repository. Follow idiomatic Go conventions as defined by the Go project and community.

### Reference Material

- [Effective Go](https://go.dev/doc/effective_go) — Canonical Go style and idioms
- [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments) — Common review feedback from the Go team
- [Organizing a Go module](https://go.dev/doc/modules/layout) — Official module layout guidance

## Principles

- **Simplicity** — Write clear, straightforward code; avoid unnecessary abstractions
- **Readability** — Code should be easy to read and understand without extensive comments
- **Composition** — Prefer composition over inheritance; use interfaces for abstraction
- **Explicit error handling** — Handle errors explicitly; do not ignore them

## Project Structure

Follow the official [Organizing a Go module](https://go.dev/doc/modules/layout) guidance. Start simple and add structure only as complexity demands it.

### Single Command CLI

```text
├── main.go                 # Entry point (package main)
├── internal/               # Private application code (not importable)
│   ├── config/
│   ├── fetcher/
│   └── output/
├── go.mod                  # Module definition
├── go.sum                  # Dependency checksums
├── justfile                # Task runner recipes
└── README.md
```

### Multi-Command CLI

```text
├── cmd/                    # Entry points (one per binary)
│   ├── fetch/
│   │   └── main.go
│   └── serve/
│       └── main.go
├── internal/               # Private application code
│   ├── config/
│   ├── fetcher/
│   └── output/
├── go.mod
├── go.sum
├── justfile
└── README.md
```

### Key Conventions

- Start with a single `main.go` at root for simple CLIs; introduce `cmd/` only when you have multiple binaries
- Use `internal/` to prevent external imports — this is enforced by the Go toolchain
- Avoid `pkg/` — prefer `internal/` for application code; if you need a reusable library, it belongs in a separate module
- Keep `main.go` thin — parse flags, wire dependencies, delegate to packages in `internal/`

Project folder organisation scales with complexity. Keep all files in a flat package when fewer than 10 source files exist. When subpackages become necessary, organise by domain responsibility: `config`, `fetcher`, `output`.

## Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Package | Short, lowercase, no underscores | `fetcher`, `config`, `api` |
| Exported function/type | PascalCase | `FetchVideoInfo`, `Config` |
| Unexported function/type | camelCase | `parseResponse`, `defaultTimeout` |
| Interface (single method) | Method name + `er` suffix | `Reader`, `Fetcher`, `Formatter` |
| Interface (multiple methods) | Descriptive noun | `VideoService`, `OutputWriter` |
| Constants | PascalCase (exported), camelCase (unexported) | `MaxRetries`, `defaultPort` |
| File names | Lowercase, underscores for separation | `video_fetcher.go`, `config_test.go` |
| Test files | `*_test.go` alongside source file | `fetcher_test.go` |

### Type Naming Suffixes

Apply these suffixes consistently for domain types:

| Suffix | Usage | Example |
|--------|-------|---------|
| `*Config` | Configuration types | `AppConfig`, `FetcherConfig` |
| `*Error` | Custom error types | `FetchError`, `ValidationError` |
| `*Option` / `*Options` | Functional option types | `FetchOption`, `ClientOptions` |
| `*Service` | Business logic orchestrators | `VideoService`, `AuthService` |
| `*Client` | External API clients | `YouTubeClient`, `HTTPClient` |
| `*Handler` | Request/event handlers | `FetchHandler`, `ErrorHandler` |

## Coding Style

### General

- Use `gofmt` / `goimports` for formatting — no exceptions
- Keep functions short and focused (prefer < 50 lines)
- Avoid `init()` functions unless absolutely necessary
- Use named return values only when they improve clarity
- Prefer early returns to reduce nesting

### Visibility

- Default to unexported. Expose only what is needed at package boundaries
- Export types and functions only when they form the package's public API
- Use `internal/` packages to prevent unintended cross-package dependencies

### Error Handling

- Always check errors: `if err != nil { return err }`
- Wrap errors with context: `fmt.Errorf("fetching video %s: %w", id, err)`
- Use `errors.Is()` and `errors.As()` for error inspection
- Define sentinel errors for expected conditions: `var ErrNotFound = errors.New("not found")`

#### Custom Error Types

Define custom error types for domains with structured error variants:

```go
type FetchError struct {
    VideoID    string
    StatusCode int
    Message    string
    Err        error
}

func (e *FetchError) Error() string {
    return fmt.Sprintf("fetch video %s: %s (HTTP %d)", e.VideoID, e.Message, e.StatusCode)
}

func (e *FetchError) Unwrap() error {
    return e.Err
}
```

### Interfaces

- Define interfaces where they are *used*, not where they are implemented
- Keep interfaces small (1-3 methods)
- Accept interfaces, return concrete types
- Compose larger interfaces from smaller ones: `type ReadWriter interface { Reader; Writer }`

### Function Signatures

- `context.Context` is always the first parameter when present
- `error` is always the last return value
- Avoid boolean parameters — use functional options or named types for clarity

### Functional Options

Use the functional options pattern for configurable constructors:

```go
type Option func(*VideoFetcher)

func WithTimeout(d time.Duration) Option {
    return func(f *VideoFetcher) {
        f.client.Timeout = d
    }
}

func WithLogger(logger *slog.Logger) Option {
    return func(f *VideoFetcher) {
        f.logger = logger
    }
}

func NewVideoFetcher(baseURL string, opts ...Option) *VideoFetcher {
    f := &VideoFetcher{
        config: FetcherConfig{BaseURL: baseURL},
        client: &http.Client{Timeout: 30 * time.Second},
        logger: slog.Default(),
    }
    for _, opt := range opts {
        opt(f)
    }
    return f
}
```

### Concurrency

- Use goroutines and channels when there's clear benefit
- Protect shared state with `sync.Mutex` or prefer channel-based communication
- Use `context.Context` for cancellation and timeouts
- Always handle context cancellation in long-running operations

### Dependencies

- Minimise external dependencies — use the standard library where possible
- Pin dependency versions via `go.sum`
- Regularly run `go mod tidy` to clean unused dependencies

## Observability

### Structured Logging

Use the `log/slog` package (Go 1.21+) for all logging. Never use `fmt.Println` or `log.Println` in production code.

```go
import "log/slog"

logger := slog.New(slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{
    Level: slog.LevelInfo,
}))

logger.Info("fetching video",
    slog.String("video_id", videoID),
    slog.Int("retry", attempt),
)

logger.Error("fetch failed",
    slog.String("video_id", videoID),
    slog.String("error", err.Error()),
)
```

### Logging Rules

- Use structured key-value pairs, not formatted strings
- Include context-relevant fields (IDs, counts, durations)
- Use appropriate log levels: `Debug`, `Info`, `Warn`, `Error`
- Never log sensitive data (API keys, tokens, credentials)

## Configuration Management

### Environment Variables

Define environment variable names as constants. Use `os.Getenv` with clear defaults or validation:

```go
const (
    envAPIKey     = "YOUTUBE_API_KEY"
    envOutputDir  = "OUTPUT_DIR"
    envMaxResults = "MAX_RESULTS"
)

func LoadConfig() (*AppConfig, error) {
    apiKey := os.Getenv(envAPIKey)
    if apiKey == "" {
        return nil, fmt.Errorf("%s must be set", envAPIKey)
    }

    maxResults := 10
    if v := os.Getenv(envMaxResults); v != "" {
        var err error
        maxResults, err = strconv.Atoi(v)
        if err != nil {
            return nil, fmt.Errorf("%s must be a valid integer: %w", envMaxResults, err)
        }
    }

    return &AppConfig{
        APIKey:     apiKey,
        OutputDir:  getEnvOrDefault(envOutputDir, "./output"),
        MaxResults: maxResults,
    }, nil
}

func getEnvOrDefault(key, fallback string) string {
    if v := os.Getenv(key); v != "" {
        return v
    }
    return fallback
}
```

## Code Documentation

Exported functions, types, and packages require documentation comments following godoc conventions.

### Guidelines

- Use `//` comments directly above the declaration (no blank line between)
- First sentence should be a complete sentence starting with the element name
- Document parameters and return values when non-obvious
- Document error conditions for functions returning `error`

```go
// FetchVideoInfo retrieves metadata for the given video ID from the YouTube API.
//
// It returns ErrNotFound if the video does not exist, or a FetchError with the
// HTTP status code for other API failures.
func FetchVideoInfo(ctx context.Context, videoID string) (*VideoInfo, error) {
    // ...
}

// VideoInfo contains metadata about a YouTube video.
type VideoInfo struct {
    ID          string        // Unique video identifier
    Title       string        // Video title
    Duration    time.Duration // Video duration
    PublishedAt time.Time     // Publication timestamp
}
```

## Patterns to Avoid

| Pattern | Risk | Alternative |
|---------|------|-------------|
| `fmt.Println` in production code | Unstructured, no levels | Use `log/slog` |
| Ignoring errors (`_ = doSomething()`) | Silent failures | Always handle or explicitly document why ignored |
| `panic()` in library code | Crashes callers | Return errors |
| Global mutable state | Race conditions, testing difficulty | Pass dependencies explicitly |
| `init()` with side effects | Hidden initialisation order | Explicit setup in `main()` |
| Returning interfaces | Couples callers to abstractions | Return concrete types |
| God packages (`utils`, `helpers`, `common`) | No cohesion | Use domain-specific packages |

## Complete Example

Demonstrates naming, structure, error handling, configuration, logging, and testing patterns:

```go
package fetcher

import (
    "context"
    "errors"
    "fmt"
    "log/slog"
    "net/http"
    "time"
)

// Sentinel errors for expected conditions.
var (
    ErrNotFound    = errors.New("not found")
    ErrRateLimited = errors.New("rate limited")
)

// FetcherConfig holds configuration for the video fetcher.
type FetcherConfig struct {
    BaseURL    string
    Timeout    time.Duration
    MaxRetries int
}

// VideoFetcher retrieves video metadata from an API.
type VideoFetcher struct {
    config FetcherConfig
    client *http.Client
    logger *slog.Logger
}

// NewVideoFetcher creates a VideoFetcher with the given configuration.
func NewVideoFetcher(cfg FetcherConfig, logger *slog.Logger) *VideoFetcher {
    return &VideoFetcher{
        config: cfg,
        client: &http.Client{Timeout: cfg.Timeout},
        logger: logger,
    }
}

// Fetch retrieves metadata for the given video ID.
//
// Returns ErrNotFound if the video does not exist. Returns ErrRateLimited
// if the API rate limit is exceeded.
func (f *VideoFetcher) Fetch(ctx context.Context, videoID string) (*VideoInfo, error) {
    if videoID == "" {
        return nil, fmt.Errorf("video ID must not be empty")
    }

    f.logger.Info("fetching video", slog.String("video_id", videoID))

    url := fmt.Sprintf("%s/videos/%s", f.config.BaseURL, videoID)
    req, err := http.NewRequestWithContext(ctx, http.MethodGet, url, nil)
    if err != nil {
        return nil, fmt.Errorf("creating request for video %s: %w", videoID, err)
    }

    resp, err := f.client.Do(req)
    if err != nil {
        return nil, fmt.Errorf("fetching video %s: %w", videoID, err)
    }
    defer resp.Body.Close()

    switch resp.StatusCode {
    case http.StatusOK:
        // parse and return
        return &VideoInfo{ID: videoID}, nil
    case http.StatusNotFound:
        return nil, ErrNotFound
    case http.StatusTooManyRequests:
        return nil, ErrRateLimited
    default:
        return nil, fmt.Errorf("unexpected status %d for video %s", resp.StatusCode, videoID)
    }
}
```
