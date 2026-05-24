# Bash Tooling Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`

## Overview

- This rule is **prescriptive**: every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide.
- A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.

## Tool Catalog

| Type      | Tool         | Purpose                                                         |
| --------- | ------------ | --------------------------------------------------------------- |
| Formatter | `shfmt`      | Format shell scripts for consistent indentation and layout      |
| Linter    | `shellcheck` | Lint shell scripts for correctness, quoting, and dialect issues |

## `shellcheck`

| Field       | Value                                                                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Purpose     | Statically analyse shell scripts for correctness, quoting, dialect compatibility, and common bash pitfalls (unset variables, masked exit codes, …). |
| Runtime     | none                                                                                                                                                |
| Config file | `.shellcheckrc` at the repository root                                                                                                              |

The recommended configuration for `shellcheck` is:

```bash
shell=bash
severity=style
enable=all
```

| Setting    | Value   | Rationale                                                                                                                                    |
| ---------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `shell`    | `bash`  | Target the bash dialect for static analysis. Locks the analyser to bash semantics regardless of the script's shebang.                        |
| `severity` | `style` | Surface diagnostics at all severity tiers including style, so subtle correctness and maintainability findings are not silently filtered out. |
| `enable`   | `all`   | Opt into all optional checks (e.g. `SC2250`, `SC2310`, `SC2312`) for stricter coverage of common bash pitfalls beyond the default rule set.  |

When a `shellcheck` rule does not apply, suppress it inline with a `# shellcheck disable=<code>` directive on the preceding line and a comment explaining why the suppression is safe. Suppression MUST be as narrow as possible (single line, not whole file) and MUST be justified.

Every installation of `shellcheck` (CLI, editor integrations, future agent integrations) MUST resolve `.shellcheckrc` at the repository root, so linting stays consistent regardless of where it runs.

## `shfmt`

| Field       | Value                                                                                                               |
| ----------- | ------------------------------------------------------------------------------------------------------------------- |
| Purpose     | Format shell scripts for consistent indentation, layout of `case` branches, and spacing of redirects and operators. |
| Runtime     | none                                                                                                                |
| Config file | `.editorconfig` (`[*.sh]` block) at the repository root                                                             |

The recommended configuration for `shfmt` is:

```ini
[*.sh]
indent_style = space
indent_size = 2
switch_case_indent = true
```

| Setting              | Value   | Rationale                                                                                                                  |
| -------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------- |
| `indent_style`       | `space` | Use spaces for indentation; hard tabs in shell scripts render inconsistently across terminals, diff tools, and renderers.  |
| `indent_size`        | `2`     | Two-space indentation keeps nested control structures readable without consuming horizontal space.                         |
| `switch_case_indent` | `true`  | Indent the body of each `case` branch under the `case` keyword, so branch boundaries are visually distinct from the block. |

`.editorconfig` is a multi-tenant configuration file that may also carry blocks for other languages in this project; the `[*.sh]` block above is the part that governs `shfmt`.

Every installation of `shfmt` (CLI, editor integrations, future agent integrations) MUST resolve the `[*.sh]` block in `.editorconfig` at the repository root, so formatting stays consistent regardless of where it runs.

## Dialect Alignment

`shfmt` resolves the shell dialect from each script's shebang line and `.shellcheckrc`'s `shell=bash` declares the same dialect for static analysis. So long as scripts begin with a `bash` shebang, both tools evaluate them under the same language rules and the formatter cannot produce output the linter rejects on dialect grounds.
