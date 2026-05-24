# Go — Testing Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

Conventions for Go test code. All conventions from [core.md](core.md) apply, including naming, error handling, and package structure.

## Test File Placement

Place unit tests in `*_test.go` files alongside the source file they exercise:

```text
internal/fetcher/
├── fetcher.go          # Source
├── fetcher_test.go     # Unit tests
```

Place integration tests in a separate `tests/` directory or use build tags to distinguish them.

## Test Naming

This project uses the convention: `Test<Function>_<Scenario>_<Expected>` using PascalCase with underscores separating the three parts.

```text
TestFetch_WhenVideoExists_ReturnsVideoInfo
TestFetch_WhenAPIReturns404_ReturnsNotFoundError
TestLoadConfig_WhenAPIKeyMissing_ReturnsError
TestNewVideoFetcher_WithDefaultConfig_SetsTimeout
```

For simple tests where the function name is sufficient:

```text
TestFetchVideoInfo
TestParseResponse
```

When using table-driven tests, the top-level test name is simpler and each subtest's `t.Run` name carries the scenario detail.

Prefer one logical assertion per test. Related assertions validating the same behaviour are acceptable.

## Table-Driven Tests

Use table-driven tests for functions with multiple input/output scenarios:

```go
func TestFetch_StatusCodes(t *testing.T) {
    tests := []struct {
        name       string
        statusCode int
        wantErr    error
    }{
        {
            name:       "not found returns ErrNotFound",
            statusCode: http.StatusNotFound,
            wantErr:    ErrNotFound,
        },
        {
            name:       "rate limited returns ErrRateLimited",
            statusCode: http.StatusTooManyRequests,
            wantErr:    ErrRateLimited,
        },
        {
            name:       "server error returns generic error",
            statusCode: http.StatusInternalServerError,
            wantErr:    nil, // check with != nil instead
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            // setup, execute, assert
        })
    }
}
```

### Table Test Conventions

- Use `tests` as the slice variable name
- Use `tt` as the loop variable name
- Use `t.Run(tt.name, ...)` for subtests
- Each table entry has a descriptive `name` field
- Use `t.Parallel()` in subtests when tests are independent

## Test Data Patterns

### Fixture Helpers

Use helper functions for test data rather than repeating inline construction:

```go
func sampleConfig() FetcherConfig {
    return FetcherConfig{
        BaseURL:    "https://api.example.com",
        Timeout:    5 * time.Second,
        MaxRetries: 3,
    }
}

func sampleVideoInfo() *VideoInfo {
    return &VideoInfo{
        ID:          "abc123",
        Title:       "Test Video",
        Duration:    10 * time.Minute,
        PublishedAt: time.Date(2026, 1, 1, 0, 0, 0, 0, time.UTC),
    }
}
```

### Test Helpers

Mark helper functions with `t.Helper()` so test failures report the caller's line:

```go
func assertNoError(t *testing.T, err error) {
    t.Helper()
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
}

func assertErrorIs(t *testing.T, got, want error) {
    t.Helper()
    if !errors.Is(got, want) {
        t.Errorf("got error %v, want %v", got, want)
    }
}
```

Inline construction is acceptable for simple one-field tests where a helper adds no clarity.

## Mocking

### Interface-Based Mocking

Define interfaces at the consumer side and create mock implementations for testing:

```go
// In production code — define interface where used
type VideoFetcher interface {
    Fetch(ctx context.Context, id string) (*VideoInfo, error)
}

// In test code — manual mock
type mockFetcher struct {
    fetchFunc func(ctx context.Context, id string) (*VideoInfo, error)
}

func (m *mockFetcher) Fetch(ctx context.Context, id string) (*VideoInfo, error) {
    return m.fetchFunc(ctx, id)
}
```

### Mocking Libraries

| Library | Usage |
|---------|-------|
| Manual mocks | Preferred for simple interfaces (1-2 methods) |
| [testify/mock](https://github.com/stretchr/testify) | Complex interfaces with multiple methods and call verification |
| [gomock](https://github.com/uber-go/mock) | Code-generated mocks from interfaces |

Use manual mocks for simple interfaces. Use testify or gomock when you need call count verification, argument matching, or have many interfaces to mock.

### HTTP Test Server

Use `net/http/httptest` for testing HTTP clients:

```go
func TestFetch_WhenAPIReturnsOK_ParsesResponse(t *testing.T) {
    server := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Content-Type", "application/json")
        w.WriteHeader(http.StatusOK)
        fmt.Fprint(w, `{"id": "abc123", "title": "Test Video"}`)
    }))
    defer server.Close()

    cfg := FetcherConfig{
        BaseURL: server.URL,
        Timeout: 5 * time.Second,
    }
    fetcher := NewVideoFetcher(cfg, slog.Default())

    info, err := fetcher.Fetch(context.Background(), "abc123")
    if err != nil {
        t.Fatalf("unexpected error: %v", err)
    }
    if info.ID != "abc123" {
        t.Errorf("got ID %q, want %q", info.ID, "abc123")
    }
}
```

## Integration Tests

### Build Tags

Use build tags to separate integration tests that require external dependencies:

```go
//go:build integration

package fetcher_test

import (
    "testing"
)

func TestFetch_Integration_LiveAPI(t *testing.T) {
    // test against live API
}
```

Run integration tests explicitly:

```bash
go test -tags=integration ./...
```

### Skipping Long Tests

For tests that are part of the normal test suite but are slow, use `testing.Short()`:

```go
func TestFetch_SlowEndpoint(t *testing.T) {
    if testing.Short() {
        t.Skip("skipping slow test in short mode")
    }
    // slow test logic
}
```

**Note:** Use build tags (`//go:build integration`) when tests require external infrastructure (APIs, databases). Use `testing.Short()` when tests are self-contained but slow. Do not combine both on the same test.

## Test Conventions

- Use `t.Parallel()` for independent tests to improve test speed
- Use `context.Background()` for test contexts; use `context.WithTimeout` when testing timeouts
- Prefer Go's built-in `testing` package assertions over third-party when possible
- Use `t.Cleanup()` for teardown instead of `defer` when cleanup order matters
- Use `t.TempDir()` for temporary file/directory needs
- Assert error messages contain relevant context, not exact strings (fragile)

## Test Commands

```bash
go test ./...                 # Run all tests
go test -v ./...              # Verbose output
go test -race ./...           # Run with race detector
go test -short ./...          # Skip long-running tests
go test -count=1 ./...        # Disable test caching
go test -run TestFetch ./...  # Run specific tests by pattern
```

## Complete Example

```go
package fetcher

import (
    "context"
    "errors"
    "fmt"
    "log/slog"
    "net/http"
    "net/http/httptest"
    "testing"
    "time"
)

func sampleConfig() FetcherConfig {
    return FetcherConfig{
        BaseURL:    "https://api.example.com",
        Timeout:    5 * time.Second,
        MaxRetries: 3,
    }
}

func TestNewVideoFetcher_WithConfig_SetsTimeout(t *testing.T) {
    cfg := sampleConfig()
    fetcher := NewVideoFetcher(cfg, slog.Default())

    if fetcher.client.Timeout != cfg.Timeout {
        t.Errorf("got timeout %v, want %v", fetcher.client.Timeout, cfg.Timeout)
    }
}

func TestFetch_StatusCodes(t *testing.T) {
    tests := []struct {
        name       string
        statusCode int
        wantErr    error
    }{
        {
            name:       "OK returns no error",
            statusCode: http.StatusOK,
            wantErr:    nil,
        },
        {
            name:       "not found returns ErrNotFound",
            statusCode: http.StatusNotFound,
            wantErr:    ErrNotFound,
        },
        {
            name:       "rate limited returns ErrRateLimited",
            statusCode: http.StatusTooManyRequests,
            wantErr:    ErrRateLimited,
        },
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            t.Parallel()

            server := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
                w.WriteHeader(tt.statusCode)
                if tt.statusCode == http.StatusOK {
                    fmt.Fprint(w, `{"id": "abc123"}`)
                }
            }))
            defer server.Close()

            cfg := FetcherConfig{BaseURL: server.URL, Timeout: 5 * time.Second}
            fetcher := NewVideoFetcher(cfg, slog.Default())

            _, err := fetcher.Fetch(context.Background(), "abc123")

            if tt.wantErr != nil {
                if !errors.Is(err, tt.wantErr) {
                    t.Errorf("got error %v, want %v", err, tt.wantErr)
                }
            } else if err != nil {
                t.Errorf("unexpected error: %v", err)
            }
        })
    }
}

func TestFetch_WhenEmptyVideoID_ReturnsError(t *testing.T) {
    cfg := sampleConfig()
    fetcher := NewVideoFetcher(cfg, slog.Default())

    _, err := fetcher.Fetch(context.Background(), "")
    if err == nil {
        t.Fatal("expected error for empty video ID")
    }
}
```
