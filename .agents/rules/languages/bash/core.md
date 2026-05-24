# Bash Core Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

Rules for Bash/Shell script development including script structure, naming conventions, and coding style.

## Principles

1. **Portability** — Scripts should work across local DevContainers, GitHub Codespaces, and CI/CD pipelines
2. **Safety** — Use strict mode and error handling
3. **Readability** — Clear structure and documentation
4. **Security** — Avoid common security pitfalls

## Script Rules

### Shebang Requirements

All scripts **MUST** start with:

```bash
#!/usr/bin/env bash
```

This format ensures portability across different systems where Bash may be installed in different locations.

### Strict Mode (Mandatory)

All scripts **MUST** include strict mode settings:

```bash
#!/usr/bin/env bash
set -euo pipefail
```

| Flag | Purpose |
|------|---------|
| `-e` | Exit immediately on error |
| `-u` | Treat unset variables as errors |
| `-o pipefail` | Return exit code of first failed command in pipeline |

#### `set -e` Pitfalls

`set -e` does **not** trigger in every context. Be aware of these silent failures:

| Pattern | Problem | Fix |
|---------|---------|-----|
| `local var=$(failing_cmd)` | `local` masks the exit code | Assign first, then declare: `local var; var=$(failing_cmd)` |
| `export var=$(failing_cmd)` | `export` masks the exit code | Assign first, then export: `var=$(failing_cmd); export var` |
| `cmd_a` &#124;&#124; `cmd_b` | Failure in `cmd_a` is handled, not fatal | Expected — but ensure `cmd_b` handles the error |
| `if failing_cmd; then ...` | Failure is tested, not fatal | Expected — this is correct conditional usage |

**Note:** Do **not** set `IFS=$'\n\t'` globally. While sometimes recommended, it silently breaks common patterns including `for` loops that rely on space splitting, `read` without explicit `IFS`, and many external tool interactions. Set `IFS` locally where needed instead:

```bash
local IFS=','
read -ra parts <<< "${csv_line}"
```

### Error Handling

```bash
# Trap for cleanup on exit, interrupt, and termination
cleanup() {
    printf 'Cleaning up...\n' >&2
}
trap cleanup EXIT INT TERM

# Error handling function
error_exit() {
    printf 'ERROR: %s\n' "$1" >&2
    exit "${2:-1}"
}
```

## File Headers

```bash
#!/usr/bin/env bash
#
# Script Name: script_name.sh
# Description: Brief description of what the script does
#
# Usage: ./script_name.sh [options] <arguments>
#
# Dependencies:
#   - dependency1
#   - dependency2
#

set -euo pipefail
```

**Note**: Author, date, and version information should be managed via Git history and release tags.

## Variable Rules

### Declaration Rules

- All variables inside functions **MUST** be declared with `local`
- Constants **MUST** use `readonly` (or `declare -r`)
- Always quote variable expansions: `"${var}"`
- Use `$(...)` for command substitution — never backticks

```bash
# Constants — immutable for the script's lifetime
readonly MAX_RETRIES=3
readonly DEFAULT_TIMEOUT=30

# Local variables inside functions
my_function() {
    local my_variable="value"
    local output
    output=$(some_command)  # assign separately to preserve exit code with set -e
}

# Exported/environment variables
export MY_ENV_VAR="value"

# Default values
: "${MY_VAR:=default_value}"
```

### Arrays

Use arrays for lists and commands with arguments:

```bash
declare -a files=("file1.txt" "file2.txt" "file with spaces.txt")

# Iterate safely — "${array[@]}" preserves elements with spaces
for file in "${files[@]}"; do
    printf 'Processing: %s\n' "${file}"
done

# Build commands safely
local -a cmd=("curl" "--silent" "--fail" "${url}")
"${cmd[@]}"
```

## Function Rules

```bash
# Use snake_case for function names
my_function() {
    local param1="$1"
    local param2="${2:-default}"

    # Function body
}

# Document complex functions
# Description: Brief description of what the function does
# Arguments:
#   $1 - Description of first argument
#   $2 - Description of second argument (optional)
# Returns:
#   0 on success, non-zero on failure
```

## Script Structure

Wrap script logic in a `main` function. This prevents issues with partial downloads, enables sourcing without side effects, and keeps the global scope clean:

```bash
#!/usr/bin/env bash
set -euo pipefail

readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

usage() {
    printf 'Usage: %s [options] <arguments>\n' "$(basename "$0")" >&2
}

main() {
    local arg="${1:?missing required argument}"

    # Script logic here
}

main "$@"
```

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Local variables | snake_case | `my_variable`, `file_path` |
| Environment variables | UPPER_SNAKE_CASE | `MY_ENV_VAR`, `API_KEY` |
| Functions | snake_case | `process_file`, `validate_input` |
| Scripts | kebab-case or snake_case | `deploy-app.sh`, `post_create.sh` |
| Constants | UPPER_SNAKE_CASE + `readonly` | `readonly MAX_RETRIES=3` |

## Coding Style

### Conditionals

Use `[[ ]]` for all conditional tests — never `[ ]` or `test`:

```bash
# ✅ Correct — [[ ]] handles spaces, globs, and regex safely
if [[ -f "${file_path}" ]]; then
    printf 'File exists: %s\n' "${file_path}"
fi

if [[ "${status}" =~ ^(success|complete)$ ]]; then
    printf 'Done\n'
fi

# ❌ Avoid — [ ] requires manual quoting and has no regex support
if [ -f $file_path ]; then ...
```

### Output

Use `printf` instead of `echo` for reliable output:

```bash
# ✅ Reliable — consistent across all environments
printf 'Processing %s of %s\n' "${current}" "${total}"
printf 'Error: %s\n' "${message}" >&2

# ❌ Avoid — echo behaviour varies (-n, -e, backslash handling)
echo "Processing ${current} of ${total}"
echo -e "Line1\nLine2"  # not portable
```

Use `echo` only for simple, unformatted, single-line output where portability is not a concern.

### Command Existence Checks

Use `command -v` to check if a command is available:

```bash
# ✅ POSIX-compliant and reliable
if ! command -v jq &> /dev/null; then
    error_exit "jq is required but not installed"
fi

# ❌ Avoid — 'which' is unreliable across systems
if ! which jq &> /dev/null; then ...
```

### Debugging

Use the `TRACE` pattern for opt-in debug output:

```bash
#!/usr/bin/env bash
set -euo pipefail
[[ "${TRACE-0}" == "1" ]] && set -x
```

Run with `TRACE=1 ./script.sh` to enable verbose tracing.

## Reference Material

- [Google Shell Style Guide](https://google.github.io/styleguide/shellguide.html)
- [BashFAQ](https://mywiki.wooledge.org/BashFAQ) and [Bash Pitfalls](https://mywiki.wooledge.org/BashPitfalls)
- [GNU Bash Manual](https://www.gnu.org/software/bash/manual/)
