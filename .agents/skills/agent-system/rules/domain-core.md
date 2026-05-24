# Domain Core Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring a per-domain `core.md` file at `$AGENT_SYSTEM_FOLDER/rules/<category>/<domain>/core.md`. Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`. For skill-bundled topic-rule `core.md` files (inside a skill's `rules/`), see `$AGENT_SYSTEM_SKILL/rules/rule.md`.

| Item | Rule |
|------|------|
| **Path** | `$AGENT_SYSTEM_FOLDER/rules/<category>/<domain>/core.md` |
| **Categories** | `languages`, `formats`, `tools` (extensible) |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/domain-core.md` |
| **Reference implementations** | `rules/languages/bash/core.md`, `rules/formats/markdown/core.md` |
| **Required H2 sections** | Variables, Overview, Principles, then domain-specific sections |
| **Heading cap** | H4 (deeper than skill rules because domain content varies) |
| **Tier** | Project rules (mutable per project — see `$AGENT_SYSTEM_SKILL/rules/conventions.md § Mutability Contract`) |

## Purpose

A domain `core.md` is the foundational rule for a language / format / tool domain. It captures overview, principles, project structure, naming conventions, coding style, and authoring conventions. It is loaded first whenever a human or agent works in that domain, with sibling files (`tooling.md`, `development-environment.md`, etc.) loaded as needed for the task.

Sibling files in the same domain folder document tooling, environment, testing, security, and other category-specific concerns. The `core.md` is the only file that EVERY task in the domain loads.

## File Structure

| Section | Required | Purpose |
|---------|----------|---------|
| `## Variables` | Yes | Inheritance declaration |
| `## Overview` | Yes | One-paragraph framing of what the domain covers |
| `## Principles` | Yes | Numbered list of foundational principles (3-5 items) |
| Domain sections | Yes | Conventions, structure, style — the substantive content |

## Rules

- The `core.md` MUST NOT duplicate content from sibling files. Tooling lives in `tooling.md`; environment lives in `development-environment.md` (and per-environment files). Cross-reference rather than restate.
- Principles use a numbered list with a bold lead-in word followed by an em-dash explanation: `1. **Portability** — Scripts should work across …`
- The Overview is one paragraph, not a section tree. Substantive content goes under the named domain sections.
- Use MUST / SHOULD / MUST NOT in **bold** when expressing requirements: `Scripts **MUST** include strict mode`.
- Code examples use fenced blocks with the language identifier (`bash`, `markdown`, `go`, …).
- Tables are used for structured rules (flag → purpose, pattern → problem → fix). Avoid prose where a table communicates the same content faster.

## Loading Contract

Every action that operates in a domain context loads `core.md` first. Sibling files are loaded only when their concern is engaged:

- Writing tests? Load `testing.md`.
- Provisioning the environment? Load `development-environment.md` plus the relevant per-environment files.
- Configuring a tool? Load `tooling.md`.

`core.md` MUST be self-contained enough to support reading and authoring the domain's primary artefacts without the siblings.

## Worked Example

`rules/languages/bash/core.md` is the worked example: Variables → Overview → Principles (Portability, Safety, Readability, Security) → Script Rules (shebang, strict mode, error handling) → Naming → Style. Tools (`shellcheck`, `shfmt`) are referenced but never configured here — they live in `rules/languages/bash/tooling.md`.
