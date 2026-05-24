# Create DevContainer

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DEVCONTAINER_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Generate a new, rules-compliant DevContainer configuration for a project.

## Flow

### Prerequisites

This action assumes all bundled rules and templates have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Determine what information is already available before deciding how to proceed.

**Scan the repository structure, conversation, and any referenced materials for:**

- Project languages, versions, and frameworks
- Build tools and task runners in use
- Existing DevContainer configuration (`.devcontainer/`)
- Any stated requirements or constraints

**Select the appropriate mode:**

| Mode          | When                                                                                                                  | Next Step                                   |
| ------------- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- |
| **Configure** | The project context is clear — languages, versions, and tools can be determined from repository files or conversation | Go to Step 2                                |
| **Discover**  | The project is new or blank — insufficient context to determine the technology stack                                  | Ask clarifying questions, then go to Step 2 |

If uncertain, default to **Discover** — it is better to confirm understanding than to assume.

In **Discover** mode, ask about:

- Primary languages and target versions
- Frameworks or libraries in use
- Build tools, linters, and task runners
- Any special environment requirements (e.g. databases, services)

Present findings (from either mode) for the user to confirm or adjust before proceeding.

### Step 2: Load Project Rules

**Goal:** Load project rules from `$AGENT_SYSTEM_FOLDER/rules/` based on the assessed project context.

Always load:

- `$AGENT_SYSTEM_FOLDER/rules/git-workflow/development-environment.md` and its sibling per-environment files (`devcontainer.md`, `vscode.md`) — git tooling and provider CLI (uses `$REPO_PROVIDER` from root `AGENTS.md`)
- `$AGENT_SYSTEM_FOLDER/rules/formats/markdown/development-environment.md` and its sibling per-environment files (`devcontainer.md`, `vscode.md`) — every project authors markdown

Then scan `$AGENT_SYSTEM_FOLDER/rules/**/development-environment.md` for files matching the detected languages and formats (e.g. Go, Bash). For each match, also load any sibling per-environment files (`devcontainer.md`, `vscode.md`) present in the same directory — these carry the declarations under the split shape (per `$DEVCONTAINER_SKILL/rules/assembly.md` § Inputs).

### Step 3: Build Configuration

**Goal:** Assemble the complete DevContainer configuration from all loaded rules.

Composition, dedup, conflict resolution, and lifecycle script filename assignment are governed by `$DEVCONTAINER_SKILL/rules/assembly.md`. Apply that rule end-to-end across all loaded contributing rules.

In addition, apply skill-owned configuration per the bundled rules:

1. **Base image** — Per `$DEVCONTAINER_SKILL/rules/core.md`
2. **Features** — Per `$DEVCONTAINER_SKILL/rules/features.md` for versioning rules; technology-specific features come from contributing rules via assembly
3. **Extensions** — Per `$DEVCONTAINER_SKILL/rules/extensions.md` for essential extensions; technology-specific extensions come from contributing rules via assembly
4. **Container settings** — Per `$DEVCONTAINER_SKILL/rules/core.md`
5. **Security settings** — Per `$DEVCONTAINER_SKILL/rules/security.md`
6. **Lifecycle hooks** — Per `$DEVCONTAINER_SKILL/rules/core.md` § Lifecycle Hooks and § Modular Script System for the structure; contributing-rule hooks are filename-assigned and ordered per `$DEVCONTAINER_SKILL/rules/assembly.md` § Lifecycle Script Filename Assignment

**Conflict surfacing.** If assembly produces one or more conflicts (per `$DEVCONTAINER_SKILL/rules/assembly.md` § Conflict Surfacing), collect them and present the full batch to the user in a single prompt before proceeding to Step 4. Apply the user's resolutions, then continue. If there are no conflicts, proceed to Step 4 directly.

### Step 4: Validate and Confirm

**Goal:** Validate the configuration and get user approval before writing files.

Validate against `$DEVCONTAINER_SKILL/rules/checklist.md` — confirm all required elements are present.

Present the complete configuration for approval, summarising:

- What was configured and why
- Included features (with versions) and extensions
- Lifecycle hook contents
- How to rebuild or customise

Do not create files until the user explicitly approves.

### Step 5: Create Files

**Goal:** Write the approved configuration files.

Create the DevContainer file structure per `$DEVCONTAINER_SKILL/rules/core.md` § File Structure:

```text
.devcontainer/
├── devcontainer.json
├── post-create.sh
├── post-start.sh
├── run-scripts.sh
├── post-create.d/
│   └── 90-run-extensions-scripts.sh
└── post-start.d/
    └── 90-run-extensions-scripts.sh
```

Contributing rules' hook scripts are written into the `.d/` directories with filenames assigned per `$DEVCONTAINER_SKILL/rules/assembly.md` § Lifecycle Script Filename Assignment.

Confirm creation and summarise next steps. The summary MUST explicitly call out any **manual user actions** required after the container starts that are not handled by lifecycle hooks — most commonly, provider CLI authentication (e.g. `gh auth login` for GitHub repo / work-tracking providers, `az login` for Azure DevOps). List the exact commands the user should run.

## Error Handling

| Scenario                        | Action                                                    |
| ------------------------------- | --------------------------------------------------------- |
| Cannot detect project languages | Ask the user to specify languages and versions            |
| Provider configuration missing  | Default to `github`                                       |
| User unsure of requirements     | Ask clarifying questions about their development workflow |
| Conflicting rules           | Flag the conflict and ask the user to resolve             |
