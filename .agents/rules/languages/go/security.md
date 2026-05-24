# Go — Security Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Prohibited Practices

| Practice | Risk | Alternative |
|----------|------|-------------|
| `os/exec` with unsanitised input | Command injection | Validate/sanitise all inputs; use argument lists, not shell strings |
| Ignoring `error` return values | Silent failures, data corruption | Always check and handle errors |
| `unsafe` package usage | Memory corruption, undefined behaviour | Use safe alternatives; document if unavoidable |
| Hardcoded credentials/secrets | Credential exposure | Use environment variables or secret management |
| `net/http` default client (no timeout) | Resource exhaustion | Always set explicit timeouts on HTTP clients |
| `math/rand` for security-sensitive values | Predictable output | Use `crypto/rand` for tokens, keys, nonces |

## Secure Practices

### Input Validation

- Validate all external inputs (CLI args, API responses, file contents)
- Use `strconv` for type-safe conversions (not `fmt.Sscanf`)
- Bound string lengths and slice sizes to prevent excessive allocation

### HTTP Clients

```go
// Always create clients with explicit timeouts
client := &http.Client{
    Timeout: 30 * time.Second,
}
```

### File Operations

- Use `os.OpenFile` with explicit permissions (e.g., `0o644` for files, `0o755` for directories)
- Validate file paths to prevent path traversal
- Use `filepath.Clean()` on any user-provided paths

### Secrets and Configuration

- Never hardcode secrets, API keys, or tokens in source code
- Use environment variables for sensitive configuration
- Do not log sensitive values; redact before logging

### Dependency Security

- Run `go mod verify` to check dependency integrity
- Use [`govulncheck`](tooling.md#govulncheck) to scan the module for known vulnerabilities; the host environment provides the binary on `PATH` (see [`development-environment.md`](development-environment.md)). Run it as:

  ```bash
  govulncheck ./...
  ```

- Review new dependencies before adding them
- Prefer standard library over third-party where possible

### Concurrency Safety

- Protect shared mutable state with `sync.Mutex` or channels
- Use `-race` flag in testing: `go test -race ./...`
- Avoid global mutable state
