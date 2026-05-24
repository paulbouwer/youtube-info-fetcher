# Domain Tooling Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring a per-domain `tooling.md` file at `$AGENT_SYSTEM_FOLDER/rules/<category>/<domain>/tooling.md`. Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`. For the development-environment family that consumes this file, see `$AGENT_SYSTEM_SKILL/rules/development-environment.md`.

| Item | Rule |
|------|------|
| **Path** | `$AGENT_SYSTEM_FOLDER/rules/<category>/<domain>/tooling.md` |
| **Template** | `$AGENT_SYSTEM_SKILL/templates/tooling.md` |
| **Reference implementation** | `rules/formats/markdown/tooling.md` (canonical), `rules/languages/bash/tooling.md` |
| **Required H2 sections** | Variables, Overview, Tool Catalog, one per-tool section |
| **Per-tool heading** | H2, backticked tool name (`` ## `shellcheck` ``) |
| **Heading cap** | H2 for tool blocks (no kind-grouping wrapper) |
| **Voice** | Passive, prescriptive (MUST / SHOULD), no orchestrator names |
| **Tier** | Project rules (mutable per project) |

## Purpose

A domain `tooling.md` is the canonical catalog of tools required to make the parent domain productive. It documents **purpose, runtime requirement, and configuration** of each tool. It says **nothing** about installation, invocation, or environment-specific delivery — those concerns belong to the development-environment family.

A clean way to remember the split: `tooling.md` says **what** and **why**; `development-environment.md` (and its per-environment siblings) say **how** and **where**.

## Top-Level Structure

```text
# <Domain> Tooling Rule
## Variables
## Overview                       (two prescriptive bullets, see below)
## Tool Catalog                   (Type | Tool | Purpose)
## `<tool-name-1>`                (backticked binary/package name, H2)
   | Field | Value |
   | Purpose / Runtime / Config file |
   <intro sentence for the configuration>
   ```<lang>
   { … recommended config … }
   ```
   | Setting/Rule | Value | Rationale |
   <closing paragraph: cross-environment config-resolution statement>
## `<tool-name-2>`                (same skeleton)
```

Per-tool blocks are **flat at H2** — there is no `## Linters` / `## Formatters` kind wrapper. Kind is captured by the `Type` column in the Tool Catalog, not by an enclosing heading. This keeps heading depth shallow and produces clean, symmetric anchors (`tooling.md#prettier`, `devcontainer.md#prettier`, `vscode.md#prettier`) across the rules-family files.

## Overview — Two Bullets, Prescriptive

Two bullets carry the load-bearing claims:

1. **Prescriptive framing.** Every tool listed below is a REQUIREMENT that any environment provisioned for this domain MUST provide. Phrased in passive voice — no orchestrator or build mechanism is named.
2. **Single-config rule.** A single configuration per tool MUST work across every environment a human or agent uses (terminal CLI, editor, future agent CLIs). Where a tool is read by both a CLI and an editor extension, both MUST resolve the same configuration file at the same path.

No additional Overview prose. The two bullets are the contract.

The single-config rule is scoped **per tool** — it constrains the configuration files that a `tooling.md` tool reads. It does **not** forbid editor-side bridge settings that influence behaviour adjacent to a tool but configure something else entirely (e.g. `editor.tabSize: 2` in a `[shellscript]` workbench block configures the editor, not the formatter).

## Tool Catalog — Pure Index

Three columns: **Type | Tool | Purpose**.

- Type — the tool's kind (`Formatter`, `Linter`, `Type checker`, `Test runner`, `Vulnerability scanner`, …)
- Tool — backticked name
- Purpose — short phrase

Do **not** add Runtime or Config-file columns — those live in the per-tool block; duplication creates a drift surface. Do **not** add a Section-link column — the backticked tool name is itself the stable anchor.

When a domain has only one or two tools, the catalog feels redundant. Keep it anyway — it earns its place once domains add validators, generators, type-checkers, etc.

## Per-Tool Block — Vertical Key/Value Table + Prose

Each tool gets an **H2** section whose heading is the backticked tool name, containing, in order:

### 1. Vertical Key/Value Table

Three rows:

| Field       | Value                                                                           |
| ----------- | ------------------------------------------------------------------------------- |
| Purpose     | One-sentence statement of what this tool does in this domain.                   |
| Runtime     | Minimum constraint, e.g. `Node ≥ 20`, or `none` for natively compiled binaries. |
| Config file | Path at the repository root, or `none`.                                         |

**Multi-tenant config files.** When a tool reads a shared config file that other domains also write into (canonical example: `.editorconfig` with `[*.sh]`, `[*.go]`, `[*.py]` blocks), qualify the `Config file` row with the section the tool reads — e.g. `` `.editorconfig` (`[*.sh]` block) at the repository root ``. The qualifier propagates into the closing cross-environment paragraph and signals that the file is not domain-owned.

The Notes field is **not** a row in the key/value table — multi-sentence content reads poorly inside table cells. Use a paragraph between the table and the configuration section instead.

### 2. Configuration Section

Always emitted — uniform shape across tools.

- **Tool with config:** an intro sentence ("The recommended configuration for `<tool>` is:"), then a fenced code block with the config, then a Setting/Rule rationale table explaining each non-default value.
- **Tool with no config:** a single sentence ("`<tool>` uses its built-in defaults; no project configuration is required.") and no code block. Emit the sentence anyway — explicit "no config" beats an absent section.

### 3. Closing Paragraph

A generic cross-environment config-resolution statement: "Every installation of `<tool>` (CLI, editor integrations, future agent integrations) MUST resolve `<config>` at the repository root, so <linting/formatting/…> stays consistent regardless of where it runs."

Restates Overview bullet 2 with the concrete config path. Do **not** name specific editor extensions, devcontainer features, or host environments — that is the development-environment family's job.

## Runtime as a Minimum Constraint

The Runtime field expresses **what the tool needs**, not **which version is installed**. Use a minimum-version constraint:

- Good: `Node ≥ 20`
- Bad: `Node 20 LTS`

Per-environment files declare the satisfying pin (e.g. `Node 20 LTS`). When two domains declare overlapping runtime requirements, the per-environment file picks a single version satisfying both — and `tooling.md` files do not need editing.

## What Stays Out

- **Installation mechanics.** No devcontainer feature URIs, no `apt` packages, no `npm install` snippets.
- **Invocation examples.** No `<tool> --foo path/to/x.md` tutorials.
- **Editor-extension names.** No `DavidAnson.vscode-markdownlint` or `esbenp.prettier-vscode`.
- **Host-environment-specific notes.** No "the community feature also installs Node transitively…" content.

If a Notes paragraph would otherwise carry one of these, drop or move it to the appropriate per-environment file.

## Aligning Formatter and Linter

If the domain has both a formatter and a linter that touch the same files, their configurations MUST be aligned so the formatter never produces output the linter rejects.

- **Per-rule alignment** — the formatter's settings cite the lint rules they align with. Markdown's `tooling.md` does this: prettier's `tabWidth: 2` mirrors markdownlint-cli2's `MD007.indent` and `MD010.spaces_per_tab`.
- **File-level alignment** — when the formatter and linter operate on disjoint axes (no per-rule overlap), capture alignment as a final `## <Something> Alignment` paragraph at the bottom of `tooling.md`, sibling to the per-tool blocks. Bash's `tooling.md` does this with `## Dialect Alignment` (shellcheck and shfmt have no overlapping rules but must agree on dialect).

## Rationale Prose Stays Domain-Agnostic

Per-setting rationale cells MUST describe **what the setting does and why** in domain-agnostic terms. They MUST NOT name specific document shapes from sibling rules (e.g. "useful for the two-layer dev-env doc's repeated subsection names") — that leaks sibling-file structure into the tooling layer and dates the rationale to a specific snapshot of an unrelated file.

Phrase rationales as general-purpose justifications: "Allow duplicate heading text where the parent heading differs — useful for documents that organise repeated blocks under multiple top-level groupings."

This is the same one-way visibility principle that bars `tooling.md` from naming orchestrators, applied to sibling rules.
