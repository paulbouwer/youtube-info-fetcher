# Synthesise Research

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$RESEARCH_SKILL` and `$GRILL_ME_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Consume one or more research dossiers and produce a summary and position document that states a position and opinion plus a Gaps / Next Research section, making no action or routing recommendations.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Collect the Dossiers

**Goal:** Identify the dossiers to synthesise and where the position document will land.

Gather the input dossiers — one or more — that the synthesis will consume. A single dossier is a valid input; the synthesis still produces a position.

Resolve the output location per `$RESEARCH_SKILL/rules/core.md § Output-Location Resolution`. The position document lives alongside the dossiers it synthesises:

| Location | Path |
|----------|------|
| Standalone | `$PROJECT_FOLDER/research/YYYY-MM-DD-<slug>/YYYY-MM-DD-summary-and-position.md` |
| Work package | `$PROJECT_FOLDER/work/<wp>/research/YYYY-MM-DD-summary-and-position.md` |

### Step 2: Read and Compare the Dossiers

**Goal:** Build an evidence-grounded view across the dossiers before forming any position.

Read each dossier in full per `$RESEARCH_SKILL/rules/citations.md § Read Sources, Do Not Guess`. Across the dossiers, identify where they agree, diverge, or contradict one another, and note the evidence behind each.

Carry every reference forward into a consolidated `[F##]` source index, deduplicated across the dossiers, per `$RESEARCH_SKILL/rules/citations.md`. Keep direct evidence separate from inference.

### Step 3: Interrogate Before Forming a Position

**Goal:** Pressure-test the emerging position before committing to it.

Before settling on a position, challenge it. When the position rests on assumptions, unresolved trade-offs, or contradictions between dossiers, resolve them by interrogation using the `grill-me` skill (`$GRILL_ME_SKILL`) — reference it; do not duplicate its questioning here.

### Step 4: Write the Summary and Position

**Goal:** Produce the position document from the template and write it to the resolved location.

Fill `$RESEARCH_SKILL/templates/summary-position.md`, completing each section:

1. **Metadata** — date, the dossiers synthesised, scope, and a consolidated source count.
2. **Executive Summary** — what the synthesis answers, the headline position, and the key cross-dossier insights.
3. **Cross-Reference Comparison** — where the dossiers agree, diverge, or contradict (optional, but the clearest way to show the basis for the position).
4. **Position and Opinion** — the reasoned stance drawn from the dossiers, separating direct evidence from inference.
5. **Gaps / Next Research** — open questions, contradictions needing more evidence, and follow-up worth doing.
6. **Sources** — the consolidated `[F##]` index.

The document states a position and an opinion only. It **MUST NOT** emit action or routing recommendations, and **MUST NOT** direct the reader into other skills — the reader decides what to do next, per `$RESEARCH_SKILL/rules/core.md § Artifact-First Output`.

Present the completed document for review. Do not write the file until the user approves. On approval, write it to the resolved location.

### Step 5: Stop at the Read-Only Boundary

**Goal:** Close out at the read-only boundary and hand back to the user.

Per `$RESEARCH_SKILL/rules/core.md § Read-Only Research Boundary`, the position document is the only output. Make no implementation edits. Confirm the document path and note any entries in its Gaps / Next Research section. The user decides what to do next.

## Error Handling

| Scenario | Action |
|----------|--------|
| No dossiers provided | Ask which dossiers to synthesise; a synthesis needs at least one |
| Dossiers contradict one another | Record the contradiction in Position and Opinion and Gaps / Next Research; do not paper over it |
| Position rests on unresolved assumptions | Pressure-test it using the `grill-me` skill (`$GRILL_ME_SKILL`) before writing |
| A dossier cannot be read | Note the gap in Gaps / Next Research; do not synthesise from an unread source |
| User asks for an action or routing recommendation | Decline per the artifact-first rule; the document states a position only |
