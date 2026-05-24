# Domain Development-Environment Asset Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Rules for authoring the development-environment family of files at `$AGENT_SYSTEM_FOLDER/rules/<category>/<domain>/`: a slim `development-environment.md` index plus per-environment sibling files (`devcontainer.md`, `vscode.md`, future hosts/workbenches). Universal rules come from `$AGENT_SYSTEM_SKILL/rules/conventions.md`. Tool requirements consumed here are catalogued by `$AGENT_SYSTEM_SKILL/rules/tooling.md`.

| Item | Rule |
|------|------|
| **Index path** | `$AGENT_SYSTEM_FOLDER/rules/<category>/<domain>/development-environment.md` |
| **Per-environment paths** | `<domain>/devcontainer.md`, `<domain>/vscode.md`, future siblings |
| **Templates** | `$AGENT_SYSTEM_SKILL/templates/{development-environment,devcontainer,vscode}.md` |
| **Reference implementations** | `rules/formats/markdown/` (canonical), `rules/languages/bash/` |
| **Heading cap** | H3 across all dev-env files |
| **Per-block heading** | H3, bare backticked name (`` ### `shellcheck` ``) |
| **Voice** | Passive, prescriptive — no orchestrator names |
| **Pinning policy** | Language runtimes MUST pin to ≥ major.minor; tools SHOULD pin |
| **Tier** | Project rules (mutable per project) |

## Purpose

The development-environment family declares **how each environment satisfies** the tool requirements catalogued in `tooling.md`. This is where install mechanisms, editor extensions, and host-vs-workbench wiring live.

A clean way to remember the split: `tooling.md` says **what** and **why**; the development-environment family says **how** and **where**.

## Two-Layer Environment Model

The family uses a two-layer model:

- **Host Environments** — execution environments that own filesystem, runtimes, and CLI tools (DevContainer today; future agent sandboxes / isolated execution environments).
- **Workbenches** — user- or agent-facing surfaces that run inside a host environment (VS Code today; future agent CLIs).

A flat enumeration of "DevContainer + VS Code + agent sandbox + …" hides that VS Code runs *inside* a host. The two-layer model makes the orchestration relationship explicit and lets each per-environment file declare host-vs-workbench-scoped satisfaction facts without ambiguity.

## File Split

A domain's development environment rule is **not** a single file. It is an index plus per-environment files in the same folder:

```text
rules/<category>/<domain>/
├── tooling.md
├── development-environment.md   ← slim index
├── devcontainer.md              ← host environment
└── vscode.md                    ← workbench
```

Future hosts (agent sandboxes, isolated execution environments) and future workbenches (agent CLIs) become new sibling files (`agent-sandbox.md`, `agent-cli.md`), not new sections in an existing file. Each per-environment file uses native vocabulary for its layer (Tool/Runtime in `devcontainer.md`, Extension/Settings in `vscode.md`) without forcing a single shared lexicon.

## Heading Discipline

- Heading depth is capped at H3 across all dev-env files. No H4 or H5.
- Block headings use the **bare backticked name** — an H3 whose text is the tool or runtime name surrounded by backticks. The kind comes from the enclosing H2 wrapper. This gives symmetric anchors across `tooling.md`, `devcontainer.md`, and `vscode.md` for the same conceptual entity.

## Per-Block Shape

Every block (Runtime, Tool, Extension) follows the same three-part shape:

1. **Vertical key/value table** with type-specific fields.
2. **Snippet** (`jsonc`) immediately below the table, when an install/feature/settings block is needed. Snippets stay out of table cells.
3. **Optional Notes prose** at the bottom for clarifications that do not fit a row.

This mirrors `tooling.md`'s block shape so all three files read as a family.

## Field Sets

| Block type | Table rows                                                                 | Snippet below                     |
| ---------- | -------------------------------------------------------------------------- | --------------------------------- |
| Runtime    | `Version`, `Feature`                                                       | `features` jsonc                  |
| Tool       | `Install`, `Runtime`, `Lifecycle hook`, `Config file`                      | `features` jsonc                  |
| Extension  | `Extension ID`, `Settings`, `Reads config from` (`N/A` for authoring aids) | `settings` jsonc when non-trivial |

### Field Semantics

- `Runtime` (in Tool block) links to the corresponding Runtime block above. The link's anchor text is the backticked runtime name; the target is the runtime's H3 anchor (e.g. `#node`). Forward declaration is the source of truth — no reverse-lookup `Required by` column on the Runtimes side.
- `Config file` (in Tool block) and `Reads config from` (in Extension block) link to the relevant `tooling.md` anchor, not just the path.
- `Lifecycle hook` resolves to one of: `none`, a named script in the project's devcontainer lifecycle hooks catalog (e.g. `10-corepack-pnpm.sh (post-create)`), or a rule-local script declared in the same file.
- `Settings: none` is a valid row value; otherwise the row says "see snippet below" and the `jsonc` snippet sits below the table.

## Pinning Policy

**Language runtimes MUST pin** explicitly to at least major.minor (e.g. `{ "version": "1.24" }` for Go, `{ "version": "22" }` for Node). Language runtimes are the foundation other tooling resolves against — an unpinned runtime silently propagates non-determinism into every dependent tool, including ones whose own pin would otherwise be honoured. Domains MUST NOT opt out of runtime pinning.

**Tools SHOULD pin** their version explicitly (e.g. `{ "version": "0.11.0" }` for shellcheck) when determinism matters. Pinning is exactly what the per-environment files exist to do: `tooling.md` declares minimum constraints; the per-environment file declares the satisfying pin.

A domain MAY accept the feature's default-resolved version for a tool (an empty option block, `{}`, or a stable token such as `"os-provided"` for OS-owned tools) when the maintenance cost of bump PRs outweighs the reproducibility benefit — typically because the feature is well-maintained with stable defaults, the tool is part of the base image, or the tool's surface area is small. Document the choice in the domain's plan or a similar narrative artefact so future readers understand it was deliberate.

## Index File Shape

`development-environment.md` is a slim index. Sections:

```text
# <Domain> Development Environment Rule
## Variables
## Overview                  (two-layer model, prescriptive framing, congruence note)
## Tool Requirements         (bullet list → tooling.md anchors)
## Host Environments
   - [DevContainer](devcontainer.md)
## Workbenches
   - [VS Code](vscode.md)
```

The Tool Requirements list is bullets only — no tool-by-environment matrix. Per-environment satisfaction is discoverable by opening the relevant per-environment file.

The Overview includes a one-sentence congruence note: not every `tooling.md` tool need appear in every per-environment file; each per-environment file declares what it provides.

## `devcontainer.md` Shape

```text
# <Domain> DevContainer Host Rule
## Variables
## Overview                  (host framing, lifecycle catalog ack, runtimes-precede-tools rule)
## Runtimes
   ### `Node`
## Tools
   ### `markdownlint-cli2`
   ### `prettier`
```

Tools are flat (no kind sub-grouping like Linters/Formatters) — kind is `tooling.md`'s responsibility; `devcontainer.md`'s job is install mechanics.

**Omit `## Runtimes` when no tool needs one.** When every tool in the domain has `Runtime: none` (statically compiled binaries — e.g. `shellcheck` ships as a Haskell binary, `shfmt` as a Go binary), the Runtimes block has no rows and MUST be omitted entirely. An empty placeholder section is noise. Base-image content (e.g. bash itself in a bash domain) is implicit to the host and not declared as a runtime.

## `vscode.md` Shape

```text
# <Domain> VS Code Workbench Rule
## Variables
## Overview                  (workbench framing, MUST/SHOULD section semantics)
## Extensions Satisfying Tool Requirements
   ### `markdownlint-cli2`
   ### `prettier`
## Authoring Aids
   ### `markdown-all-in-one`
```

Two H2 sections carry different prescriptive levels: satisfying-requirements extensions MUST be installed (each pairs with a `tooling.md` entry); authoring-aid extensions SHOULD be installed (recommended but no `tooling.md` counterpart). Editor-only extensions land in `## Authoring Aids` without forcing a synthetic `tooling.md` entry.

### Settings Minimalism

Each Extension's `Settings` snippet MUST contain only settings that materially differ from the extension's own defaults. Restating an extension default (`shellcheck.enable: true`, `shellcheck.run: onSave` when `onSave` is the default, etc.) adds a drift surface and noise without functional value — `Settings: none` beats a snippet that restates defaults.

When a snippet is needed, include the genuine bridge settings (`editor.defaultFormatter`, `editor.formatOnSave`, language-scoped editor-typing settings) and nothing else.

**Rehousing vs behaviour change.** Dropping a setting that restates a default is rehousing (no behaviour change); dropping a setting that overrode a default is a behaviour change and MUST be flagged in the plan as such, not buried under "shape cleanup".

## One-Way Visibility

Rule files MUST NOT name orchestrators (e.g. "the devcontainer skill", "the assembly stage") or specific build mechanisms. Visibility is one-way: orchestrators read rules, never the reverse. Rules declare requirements in passive voice ("the host MUST provide", "the workbench MUST install"). This keeps rules stable as orchestrators change.

## Congruence Rule

None. Each per-environment file declares what it provides. A `tooling.md` tool may be CLI-only (no entry in `vscode.md`), extension-only (no entry in `devcontainer.md`), or present in both. The index's Tool Requirements list is the cross-environment map; the per-environment files are the source of truth for satisfaction.

## Multi-Tenant Config Files

Some `tooling.md` files qualify their `Config file` row with a section qualifier (e.g. `` `.editorconfig` (`[*.sh]` block) ``). When two or more domains declare blocks against the same physical file, the per-environment files MUST reflect that the file is shared — typically by surfacing the qualifier in the `Config file` / `Reads config from` rows. Two domains writing different settings to the same glob (e.g. `[*]` blocks with conflicting `indent_size`) is a defect to be flagged.
