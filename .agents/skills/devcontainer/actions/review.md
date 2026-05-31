# Review DevContainer

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Analyse an existing DevContainer configuration for rules compliance, security, and performance, then provide actionable recommendations.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Locate and Context

**Goal:** Find the DevContainer configuration, understand the project's technology stack, and load project rules.

Find DevContainer files:

| File              | Location                          | Required        |
| ----------------- | --------------------------------- | --------------- |
| devcontainer.json | `.devcontainer/devcontainer.json` | Yes             |
| post-create.sh    | `.devcontainer/post-create.sh`    | Check existence |
| post-start.sh     | `.devcontainer/post-start.sh`     | Check existence |
| run-scripts.sh    | `.devcontainer/run-scripts.sh`    | Check existence |
| post-create.d/    | `.devcontainer/post-create.d/`    | Check existence |
| post-start.d/     | `.devcontainer/post-start.d/`     | Check existence |

If no configuration is found, recommend the Create action instead.

Detect project technologies (languages, formats) from repository files, then load project rules from `$AGENT_SYSTEM_FOLDER/rules/`:

Always load:

- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/development-environment.md` and its sibling per-environment files (`devcontainer.md`, `vscode.md`) — git tooling and provider CLI (uses `$REPO_PROVIDER` from root `AGENTS.md`)
- `$AGENT_SYSTEM_FOLDER/rules/formats/markdown/development-environment.md` and its sibling per-environment files (`devcontainer.md`, `vscode.md`) — every project authors markdown

Then scan `$AGENT_SYSTEM_FOLDER/rules/**/development-environment.md` for files matching detected technologies. For each match, also load any sibling per-environment files (`devcontainer.md`, `vscode.md`) present in the same directory — these carry the declarations under the split shape (per `$DEVCONTAINER_SKILL/rules/assembly.md` § Inputs).

### Step 2: Analyse

**Goal:** Evaluate the configuration against all loaded rules.

#### Re-Run Assembly

Per `$DEVCONTAINER_SKILL/rules/assembly.md`, run the same assembly process the `create` action uses against the loaded contributing rules. This produces an **expected** `devcontainer.json` and an expected set of `.d/` script filenames and contents.

Diff the expected result against the actual `.devcontainer/` on disk and categorise findings:

| Finding        | Meaning                                                                                                                                                                                                                                                                                                  |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Missing        | Declared by a contributing rule but absent from the actual config                                                                                                                                                                                                                                    |
| Extra / orphan | Present in the actual config but not declared by any loaded contributing rule or skill-owned rule. Skill-owned files (orchestrators, `run-scripts.sh`, `90-run-extensions-scripts.sh`, `devcontainer.json` skeleton keys) are NEVER orphans — they belong to the skill skeleton                  |
| Differing      | Identifier present on both sides with different option objects, script bodies, or filenames. **Pure rename** of an unchanged hook script (same purpose, same body, only the assigned `NN-` prefix changed because the set of contributing rules changed) is downgraded to a **Low**-severity finding |
| Conflict       | Two or more contributing rules declared the same identifier with non-empty differing options (per `$DEVCONTAINER_SKILL/rules/assembly.md` § Conflict Surfacing); collect for reporting                                                                                                           |

#### Structural Validation

Evaluate against `$DEVCONTAINER_SKILL/rules/checklist.md` — walk through each section:

- Base image compliance
- Features and explicit versioning
- Essential extensions
- Lifecycle hook structure (orchestrators, runner, user-extension hook, modular `.d` pattern)
- Container settings

#### Security Analysis

Evaluate against `$DEVCONTAINER_SKILL/rules/security.md`:

| Severity | Checks                                            |
| -------- | ------------------------------------------------- |
| Critical | Non-root user, privileged mode, hardcoded secrets |
| High     | Host mounts, base image                           |
| Medium   | Feature versions, port forwarding                 |

#### Performance Analysis

| Factor               | Question                                                                                                          |
| -------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Redundant features   | `common-utils` feature added? (Note: `git` feature is acceptable per `$DEVCONTAINER_SKILL/rules/features.md`) |
| Unnecessary features | Any features that could be removed?                                                                               |
| Unused extensions    | Any extensions not needed?                                                                                        |
| Hook optimisation    | Could lifecycle hooks be faster or more modular?                                                                  |

### Step 3: Report

**Goal:** Present findings in a clear, prioritised report.

#### Findings

Categorise all findings by severity:

| Severity | Category                                                                                          |
| -------- | ------------------------------------------------------------------------------------------------- |
| Critical | Security issues requiring immediate action                                                        |
| High     | Rules compliance violations, including missing/extra/differing items from re-running assembly |
| Medium   | Performance and versioning improvements; conflicts between contributing rules                 |
| Low      | Nice-to-have enhancements                                                                         |

Lead with the most critical findings. For each finding, state what is wrong, which rule section it violates, and the specific fix.

#### Compliance Rubric

Rate each area using a qualitative rubric:

| Area            | Compliant                                | Partial                                 | Non-Compliant                |
| --------------- | ---------------------------------------- | --------------------------------------- | ---------------------------- |
| Base Image      | Correct image used                       | Correct family, wrong tag               | Wrong image entirely         |
| Features        | All versioned, no redundancies           | Some missing versions                   | Missing required features    |
| Extensions      | All essential + language/format present  | Essential present, language/format gaps | Missing essential extensions |
| Security        | All critical/high checks pass            | Critical pass, some high gaps           | Critical failures present    |
| Lifecycle Hooks | Modular script system, correct structure | Scripts present, not modular            | Missing or incorrect hooks   |

Present the rubric as a summary table, then list specific findings grouped by area.

## Error Handling

| Scenario                            | Action                                                                       |
| ----------------------------------- | ---------------------------------------------------------------------------- |
| No DevContainer configuration found | Recommend the Create action                                                  |
| Provider configuration missing      | Note as a finding; assume `github` for analysis                              |
| Cannot detect project languages     | Ask the user to specify; review may be incomplete without language rules |
