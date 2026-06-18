# Create Research

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$RESEARCH_SKILL` and `$GRILL_ME_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Resolve the output location, run research across code, docs, and web sources in the selected mode, and write one cited dossier from the template, stopping at the read-only boundary.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Resolve Output Location

**Goal:** Determine where the dossier will land before any research begins.

Resolve the location per `$RESEARCH_SKILL/rules/core.md § Output-Location Resolution`:

| Signal | Resolution |
|--------|------------|
| User names a target work package | Work-package path: `$PROJECT_FOLDER/work/<wp>/research/` (flat) |
| User asks for standalone research | Standalone dated folder: `$PROJECT_FOLDER/research/YYYY-MM-DD-<slug>/` |
| No explicit intent, but inside or linked to a `$PROJECT_FOLDER/work/<wp>` context | Work-package path |
| No explicit intent and no work-package context | Standalone dated folder |

Explicit user intent always wins over context detection. If intent is absent and context is ambiguous, confirm the location with the user rather than guessing.

### Step 2: Scope the Research and Select Mode

**Goal:** Establish a clear topic and scope, and choose the research mode.

Establish the topic, the scope (what is covered and what is excluded), and the evidence sources in play. When scope or requirements are ambiguous and the answer is not in the sources, resolve the ambiguity by interrogation using the `grill-me` skill (`$GRILL_ME_SKILL`) — reference it; do not duplicate its questioning here.

Select the mode per `$RESEARCH_SKILL/rules/core.md § Research Modes`:

| Mode | When | Selection |
|------|------|-----------|
| `single` | Default. The topic is a single coherent line of investigation | Use unless the user describes independent lenses |
| `focused-swarm` | The user describes a small set of independent lenses or views worth researching separately | Capture the lenses the user names |

`full-swarm` is deferred and **MUST NOT** be offered as a selectable mode. If the user asks for it, explain that it is not yet available and offer `focused-swarm` instead.

### Step 3: Gather Evidence

**Goal:** Collect cited evidence from the sources in scope.

Gather evidence from the source families in scope, recording each source per `$RESEARCH_SKILL/rules/citations.md`:

| Source | What to read | Reference format |
|--------|--------------|------------------|
| Code | Workspace source, configuration, build files | Plain path with a line range |
| Docs | Repository and project documentation | Plain path with a line range |
| Web | External resources and references | Markdown link to the URL |

- Read each source before citing it — never cite from memory or assumption.
- Assign each source a stable `[F##]` identifier and reference it inline at the point of use.
- Keep direct evidence separate from inference.
- When a source cannot be read, record the gap rather than citing it speculatively.

In `focused-swarm`, research each lens independently and capture its findings with citations before synthesis.

### Step 4: Synthesise Findings into One Dossier

**Goal:** Consolidate the evidence into a single dossier structure.

Organise the findings into the dossier structure required by `$RESEARCH_SKILL/rules/dossier.md`. For `focused-swarm`, synthesise the per-lens findings into **one** dossier per `$RESEARCH_SKILL/rules/core.md § Focused-Swarm Capture and Synthesis`; the combined source index carries every reference used across all lenses, deduplicated.

### Step 5: Write the Dossier

**Goal:** Produce the dossier from the template and write it to the resolved location.

Fill `$RESEARCH_SKILL/templates/research-dossier.md`, completing every required section per `$RESEARCH_SKILL/rules/dossier.md`:

1. **Filename:** `YYYY-MM-DD-<topic>.md` (lowercase, hyphens).
2. **Location:** the path resolved in Step 1.
3. **Citations:** a mandatory `[F##]` source index, each entry referenced inline.

Present the completed dossier for review. Do not write the file until the user approves. On approval, create the resolved directory if it does not exist and write the dossier.

### Step 6: Stop at the Read-Only Boundary

**Goal:** Close out at the read-only boundary and hand back to the user.

Per `$RESEARCH_SKILL/rules/core.md § Read-Only Research Boundary`, the dossier is the only output. Make no implementation edits and no action or routing recommendations. Confirm the dossier path and note any entries in its Gaps / Next Research section. The user decides what to do next.

## Error Handling

| Scenario | Action |
|----------|--------|
| Scope or requirements ambiguous, not answerable from sources | Resolve by interrogation using the `grill-me` skill (`$GRILL_ME_SKILL`) |
| Output location ambiguous (no explicit intent, unclear context) | Confirm the location with the user before researching |
| User requests `full-swarm` | Explain it is deferred and not selectable; offer `focused-swarm` |
| A source cannot be read | Record it in the dossier's Gaps / Next Research section; do not cite it speculatively |
| User asks for implementation edits during research | Decline per the read-only boundary; the user decides next steps after the dossier is written |
| Filename conflict at the resolved location | Construct a new, unique topic slug for the `YYYY-MM-DD-<topic>.md` pattern |
