# Rules Index

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$RESEARCH_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## How to Use This Index

This file is a lazy-loading index for the skill rules bundled under `$RESEARCH_SKILL`. Identify the rule whose `Load when` matches your task, follow the link, and load only the matched file.

The folder is flat (no category sub-folders); this index is therefore a file map.

## Rules

| Rule | Purpose | Load when |
|------|---------|-----------|
| [`core.md`](core.md) | Artifact-first output, read-only research boundary, research modes, output-location resolution, completion criteria | Running either capability (Create Research or Synthesise Research) |
| [`citations.md`](citations.md) | Mandatory `[F##]` source index, local and external reference formats, read-don't-guess, evidence vs inference | Producing or reviewing any cited research artifact |
| [`dossier.md`](dossier.md) | Required dossier sections, `YYYY-MM-DD-<topic>.md` naming, standalone vs work-package placement | Writing or reviewing a research dossier |

## Non-Overridable Rules

These rules win regardless of any project-rule counterpart. Project rules covering the same area MUST defer.

- [`citations.md`](citations.md) — Citation integrity (the `[F##]` source index and read-don't-guess) is the basis on which research artifacts are trusted; weakening it would let prose confidence replace verifiable evidence.
- [`core.md`](core.md) § Read-Only Research Boundary — The boundary that keeps research from making implementation edits is invariant; only this section of `core.md` is non-overridable, while the modes and output-location policy remain overridable.
