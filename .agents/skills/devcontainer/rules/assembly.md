# DevContainer Assembly Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Overview

The devcontainer skill assembles a single `devcontainer.json` (and the surrounding `.devcontainer/` file structure) by composing requirements declared across multiple loaded contributing rules.

This rule defines **how** the skill performs that composition: how it identifies what each contributing rule declares, how it dedupes overlapping declarations, how it resolves conflicts, and how it assigns lifecycle script filenames.

Contributing rules declare **what they need**. They do not — and must not — describe ownership, deferral, or load order. All composition logic lives here.

## Principles

1. **Rules declare; the skill assembles.** Contributing rules are pure declarations of need.
2. **Deterministic output.** Given the same inputs, assembly produces the same `devcontainer.json` and the same lifecycle script filenames.
3. **Conflicts surface, not silently resolve.** When two rules genuinely disagree, the skill asks the user.
4. **Single source of truth.** Both the `create` and `review` actions consume this rule; they never duplicate the rules.

## Inputs

The skill's actions load contributing rules into context per their own instructions (see `$DEVCONTAINER_SKILL/actions/create.md` § Step 2 and `$DEVCONTAINER_SKILL/actions/review.md` § Step 1).

A **contributing rule** is one of two shapes per domain:

- **Single-file shape (legacy).** A single `development-environment.md` carries all declarations for the domain.
- **Split shape (new).** A slim `development-environment.md` index plus sibling per-environment files (`devcontainer.md` for the host, `vscode.md` for the workbench). Declarations live in the per-environment files; the index carries no declarations. The skill MUST load the per-environment sibling files when present and SHOULD treat the index as informational.

For a domain using the split shape, both `devcontainer.md` and `vscode.md` (when present) are independent contributing rules and are assembled into the same `devcontainer.json` via the same dedup rules. Their canonical paths (used for ordering — see § Lifecycle Script Filename Assignment) are the file paths relative to `$AGENT_SYSTEM_FOLDER/rules/`, not the index's path.

Treat each loaded contributing rule as one that may declare any of:

| Declaration kind               | How to recognise it                                                                                                                                                                           |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DevContainer **feature**       | A reference of the form `ghcr.io/<org>/features/<name>:<major>` appearing in a `jsonc` block under a Features-style heading, in a Runtimes block, or in prose stating the feature is required |
| VS Code **extension**          | A `publisher.extensionId` appearing under an Extensions-style heading, an extensions table, or a workbench tool's **Extension:** field                                                        |
| VS Code **setting**            | A key under `customizations.vscode.settings` in a `jsonc` block, a workbench tool's **Settings:** field, or referenced explicitly in prose                                                    |
| Lifecycle **hook requirement** | A purpose, an ordering hint (`early` / `normal` / `late`), and a script body declared under a Lifecycle Hook Requirements heading or a host-environment tool's **Lifecycle hook:** field      |
| Container-level **setting**    | Any other top-level `devcontainer.json` key (e.g. `mounts`, `forwardPorts`) declared in a `jsonc` block                                                                                       |

The contributing rule's path (e.g. `git-workflow/devcontainer.md` or `formats/markdown/devcontainer.md` under the split shape) is the rule's identifier for ordering purposes.

### Supported Document Shapes

Contributing rules MAY use any of three organisational shapes; the assembler extracts identical declarations from all and dedupes uniformly:

- **Legacy single-file shape.** A single `development-environment.md` whose top-level headings group declarations by kind (e.g. `## DevContainer Features`, `## VS Code Extensions`, `## VS Code Settings`, `## Lifecycle Hook Requirements`).
- **Two-layer single-file shape.** A single `development-environment.md` with top-level `## Host Environments` and `## Workbenches` sections each containing per-environment subsections; host-environment subsections open with a `#### Runtimes` block and a `#### Tools` block whose per-tool subsections use bold-label fields (`**Install:**`, `**Runtime:**`, `**Lifecycle hook:**`, `**Config file:**`, `**Notes:**`); workbench subsections contain per-tool blocks with `**Extension:**`, `**Settings:**`, `**Reads config from:**`, `**Notes:**`.
- **Split shape (new).** A slim `development-environment.md` index plus sibling `devcontainer.md` and/or `vscode.md` files. The host file (`devcontainer.md`) carries `## Runtimes` and `## Tools` H2 sections, with each runtime and tool as an H3 subsection whose heading is the backticked tool/runtime name (for example, the heading text is `Node` or `markdownlint-cli2`, surrounded by backticks); each block opens with a vertical key/value table whose rows are `Install`, `Runtime`, `Lifecycle hook`, `Config file` (for tools) or `Version`, `Feature` (for runtimes), followed by an optional `jsonc` snippet and optional notes prose. The workbench file (`vscode.md`) carries `## Extensions Satisfying Tool Requirements` and `## Authoring Aids` H2 sections, with each extension as an H3 subsection whose heading is the backticked extension key; each block opens with a vertical key/value table whose rows are `Extension ID`, `Settings`, `Reads config from`, followed by an optional `jsonc` settings snippet and optional notes prose.

All three shapes are first-class. The assembler walks whichever structure is present, gathers features from anywhere they appear (including Runtimes blocks, per-tool **Install:** fields, and per-tool `Install`-row tables), gathers extensions and settings from anywhere they appear (including per-tool **Extension:** / **Settings:** fields and per-extension `Extension ID` / `Settings` rows), and applies the dedup rules below identically.

## Dedup Rules

Apply these rules per declaration kind. The unit of dedup is the **identifier**:

| Kind                    | Identifier                                                                                                            |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Feature                 | The full feature reference up to and including the major version (e.g. `ghcr.io/devcontainers/features/github-cli:1`) |
| Extension               | The `publisher.extensionId`                                                                                           |
| VS Code setting         | The dotted setting key (e.g. `editor.tabSize`)                                                                        |
| Lifecycle hook          | The script's logical purpose (declared by the contributing rule); the skill assigns the filename                  |
| Container-level setting | The top-level `devcontainer.json` key                                                                                 |

### Feature, extension, and setting dedup

For each identifier, collect every declaration across all contributing rules, then resolve as follows:

| Case                                                                                                 | Resolution                                                                                |
| ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| Identifier appears once                                                                              | Use the declaration as-is.                                                                |
| Identifier appears multiple times, all with empty options (`{}`) or no options                       | Emit one declaration with `{}`.                                                           |
| Identifier appears multiple times; exactly one declaration has non-empty options, the rest are empty | Emit the non-empty declaration. Empty `{}` is treated as "no opinion".                    |
| Identifier appears multiple times with **two or more differing non-empty option sets**               | **Conflict.** Defer to § Conflict Surfacing. Do not attempt to deep-merge option objects. |
| Identifier appears multiple times with **identical non-empty option sets**                           | Emit one declaration.                                                                     |

For extensions, the "options" comparison is trivially "same ID" → dedupe to one entry.

### Container-level setting dedup

Container-level keys (e.g. `mounts`, `forwardPorts`, `containerEnv`) follow the same rules. The key itself is the identifier; option sets are the declared values. List-valued keys (such as `mounts`) are compared as sets — element order is not significant for dedup, but two non-empty differing sets for the same key are a conflict and must be surfaced.

### Feature major-version cross-check

Two declarations whose feature reference differs only by the major-version suffix (e.g. `ghcr.io/devcontainers/features/github-cli:1` and `ghcr.io/devcontainers/features/github-cli:2`) are **not** the same identifier per the dedup table, but they are almost certainly an unintentional collision. Treat any such pairing as a **conflict** and surface it per § Conflict Surfacing rather than silently emitting both.

### Lifecycle hook dedup

Hook requirements are deduped by their declared purpose (a short identifier the rule assigns, e.g. `git-safe-directory`, `allowed-signers`).

**Purpose naming convention.** Purposes MUST be specific enough that an accidental collision between unrelated rules is unlikely. Use a domain-prefixed kebab-case identifier (e.g. `git-safe-directory`, `go-tools-install`, `just-completions`) rather than generic words like `setup` or `install`. When two rules declare the same purpose unintentionally, the dedup rules below treat that as a conflict — but the convention exists to prevent the situation from arising in the first place.

| Case                                                                              | Resolution                                   |
| --------------------------------------------------------------------------------- | -------------------------------------------- |
| Purpose appears once                                                              | Emit a script for it.                        |
| Purpose appears multiple times with identical script bodies and ordering hints    | Emit one script.                             |
| Purpose appears multiple times with **differing script bodies or ordering hints** | **Conflict.** Defer to § Conflict Surfacing. |

Two hooks with **different purposes but overlapping intent** (e.g. both rules independently want to ensure `gh` is on PATH) are **not** auto-detected as conflicts. Semantic dedup is the responsibility of the rules' authors. The skill only dedupes by declared purpose.

## Intra-file Runtime Reference Consistency

Under the two-layer single-file shape and the split shape, a per-tool block in a host environment context MAY declare a runtime reference (`**Runtime:** <name>` in the legacy bold-label form, or a `Runtime` table row in the split shape) to indicate that the tool depends on a runtime supplied by that environment. Every such reference MUST resolve by **exact, case-sensitive token match** to:

- (Two-layer single-file shape) the **Runtime** column of an entry in the same Host Environment subsection's `#### Runtimes` block.
- (Split shape) the H3 heading of an entry under `## Runtimes` in the same `devcontainer.md` file.

The runtime token is a bare canonical name (e.g. `Node`, `Go`, `Python`). Versions, channels, and feature URIs MUST NOT appear in the tool block's runtime reference; they are declared exactly once, in the Runtimes block.

| Case                                                                                                                                                      | Resolution                                                                                                                                                                                                                                                                                                                          |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tool's runtime reference matches a runtime declared in the same file's Runtimes block (or, for the two-layer shape, the same Host Environment subsection) | OK.                                                                                                                                                                                                                                                                                                                                 |
| Tool declares `Runtime: none` (or `**Runtime:** none`)                                                                                                    | OK. No reference is made.                                                                                                                                                                                                                                                                                                           |
| Tool's runtime reference does NOT match any runtime declared in the same file (or same Host Environment subsection)                                       | **Hard assembly failure.** Abort assembly. Report the tool block making the reference, the file (and host environment subsection, where applicable) it sits within, and the runtimes that ARE declared. The contributing rule must be amended before assembly can proceed; this is a defect in the rule, not a user choice. |

This check is intra-file and (for the two-layer single-file shape) intra-host-section only. Cross-file runtime collapsing — e.g. two rules both declaring Node — is handled by the existing feature dedup rules (the runtime is declared as a feature URI in each file's Runtimes block, and the dedup table collapses identical URIs and surfaces major-version conflicts).

The legacy single-file shape has no Runtimes concept; this rule does not apply to it.

## Conflict Surfacing

When dedup produces one or more conflicts, the skill **collects all conflicts during assembly** and presents them in a single batch to the user **at the end of assembly, before the action's confirmation step**.

For each conflict, present:

- The identifier (feature reference, extension ID, setting key, or hook purpose)
- The contributing rules involved (by path)
- The differing values (option objects, script bodies, or ordering hints)
- A request to choose one, or to provide a reconciled value

If there are zero conflicts, no prompt is shown and the action proceeds normally.

The skill does not silently apply precedence based on load order, alphabetical position, or any other implicit heuristic. Conflicts are always surfaced.

Unresolved-runtime-reference defects (§ Intra-file Runtime Reference Consistency) are **not** conflicts — they are hard assembly failures and abort the action before the conflict-batch prompt is shown.

## Lifecycle Script Filename Assignment

Contributing rules declare hook requirements with a **purpose**, an **ordering hint**, and a **script body**. The skill assigns the actual filename and numeric prefix.

### Ordering Bands

| Band         | Prefix range | Use for                                                                                    |
| ------------ | ------------ | ------------------------------------------------------------------------------------------ |
| `early`      | `00`–`29`    | Setup that must happen before main configuration (e.g. git safe directory, signing config) |
| `normal`     | `30`–`69`    | Main setup work (e.g. installing project tooling)                                          |
| `late`       | `70`–`89`    | Setup that depends on prior steps completing (e.g. final checks, post-install caches)      |
| **Reserved** | `90`–`99`    | Skill-owned user-extension hook (`90-run-extensions-scripts.sh`)                           |

### Within-Band Ordering

Within a band, scripts are ordered **alphabetically by the contributing rule's canonical path**.

The canonical path is the POSIX-form path **relative to `$AGENT_SYSTEM_FOLDER/rules/`**, without leading slash and without a `.md` extension. For example:

| Actual file                                                              | Canonical path used for ordering       |
| ------------------------------------------------------------------------ | -------------------------------------- |
| `$AGENT_SYSTEM_FOLDER/rules/git-workflow/devcontainer.md`            | `git-workflow/devcontainer`            |
| `$AGENT_SYSTEM_FOLDER/rules/git-workflow/vscode.md`                  | `git-workflow/vscode`                  |
| `$AGENT_SYSTEM_FOLDER/rules/languages/go/development-environment.md` | `languages/go/development-environment` |
| `$AGENT_SYSTEM_FOLDER/rules/tools/just/devcontainer.md`              | `tools/just/devcontainer`              |
| `$AGENT_SYSTEM_FOLDER/rules/tools/just/vscode.md`                    | `tools/just/vscode`                    |
| `$AGENT_SYSTEM_FOLDER/rules/formats/markdown/devcontainer.md`        | `formats/markdown/devcontainer`        |
| `$AGENT_SYSTEM_FOLDER/rules/formats/markdown/vscode.md`              | `formats/markdown/vscode`              |

Rules earlier in alphabetical order receive lower prefixes within the band.

If a single rule contributes multiple hooks in the same band, those hooks are assigned consecutive prefixes in the order they appear in the rule.

### Filename Format

`NN-<purpose>.sh` where `NN` is the assigned two-digit prefix and `<purpose>` is the kebab-case purpose identifier declared by the contributing rule.

### Example

Given two contributing rules:

- `git-workflow/devcontainer.md` declares two `early` hooks: `git-safe-directory` and `allowed-signers`.
- `languages/go/development-environment.md` declares one `normal` hook: `install-go-tools`.

Assembly produces:

```text
.devcontainer/post-create.d/
├── 00-git-safe-directory.sh        (early, git-workflow, first in band)
├── 01-allowed-signers.sh           (early, git-workflow, second in band)
├── 30-install-go-tools.sh          (normal, languages/go)
└── 90-run-extensions-scripts.sh    (skill core)
```

If `languages/bash/development-environment.md` later contributed an `early` hook `install-shellcheck`, it would receive prefix `02` (after the two git-workflow entries, because `git-workflow` < `languages/bash` alphabetically).

## Determinism Guarantees

For a fixed set of contributing rules and a fixed `$REPO_PROVIDER` / `$WORK_TRACKING_PROVIDER`:

- The assembled `devcontainer.json` is byte-stable modulo formatting.
- Lifecycle script filenames and prefixes are stable.
- Conflict prompts are stable in content and order.

Adding or removing a contributing rule may shift within-band script prefixes (because alphabetical ordering changes); this is expected and acceptable.

## Orchestrator → Script Contract

Lifecycle hook scripts in `.d/` directories are invoked by the `post-create.sh` and `post-start.sh` orchestrators. The orchestrator contract is defined in `$DEVCONTAINER_SKILL/rules/core.md` § Modular Script System § Orchestrator Contract.

In summary, the orchestrator parses `--workspace-folder <path>` from its argv and exports `WORKSPACE_FOLDER` into the environment of every `.d/` script it runs. Contributing-rule hook bodies MAY rely on `${WORKSPACE_FOLDER}` being set; they MUST NOT rely on `${containerWorkspaceFolder}` (a build-time devcontainer.json substitution that does not exist at script runtime).

## Use by Actions

- `$DEVCONTAINER_SKILL/actions/create.md` § Step 3 performs assembly per this rule, then surfaces any conflicts before Step 4 confirmation.
- `$DEVCONTAINER_SKILL/actions/review.md` performs assembly per this rule against the loaded contributing rules, then diffs the result **plus the skill-owned skeleton** (per `$DEVCONTAINER_SKILL/rules/core.md` § File Structure and § Mandatory Scripts) against the actual `.devcontainer/` on disk. Skill-owned files (`devcontainer.json` skeleton keys, orchestrator scripts, `run-scripts.sh`, `90-run-extensions-scripts.sh`) are part of the expected set and MUST NOT be reported as orphans.
