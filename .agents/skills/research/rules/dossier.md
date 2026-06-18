# Research Dossier Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$RESEARCH_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Structure, naming, and placement for a single-topic research dossier.

| Item | Rule |
|------|------|
| **Template** | `$RESEARCH_SKILL/templates/research-dossier.md` |
| **Filename** | `YYYY-MM-DD-<topic>.md` (lowercase, hyphens) |
| **Standalone placement** | `$PROJECT_FOLDER/research/YYYY-MM-DD-<slug>/YYYY-MM-DD-<topic>.md` |
| **Work-package placement** | `$PROJECT_FOLDER/work/<wp>/research/YYYY-MM-DD-<topic>.md` (flat) |
| **Content** | Identical in both locations — only the path differs |

## Required Sections

A dossier **MUST** contain the following sections, in order. The matching template at `$RESEARCH_SKILL/templates/research-dossier.md` carries each one.

1. **Metadata** — topic, date, mode (`single` or `focused-swarm`), scope, and a source count.
2. **Executive Summary** — what the research answers and the headline finding, in brief.
3. **Key Insights** — the load-bearing findings as a short list, each cited.
4. **Findings** — the detailed body, organised by sub-topic or lens, every claim cited per `$RESEARCH_SKILL/rules/citations.md`.
5. **Alternatives Considered** — each option evaluated, with the selected and rejected choices and the rationale for each.
6. **Evidence vs Inference** — a clear separation of what the sources directly show from what the author infers.
7. **Gaps / Next Research** — open questions, sources that could not be read, and follow-up worth doing.
8. **Sources** — the mandatory `[F##]` source index per `$RESEARCH_SKILL/rules/citations.md`.

For a `focused-swarm` dossier, the Findings section captures each lens's findings and the Sources index carries every reference used across all lenses, deduplicated (see `$RESEARCH_SKILL/rules/core.md § Focused-Swarm Capture and Synthesis`).

## Naming Convention

**Format:** `YYYY-MM-DD-<topic>.md`

- ISO 8601 date prefix (`YYYY-MM-DD`).
- Lowercase with hyphens only; no spaces or underscores.
- A short, descriptive topic slug.

Good: `2026-04-05-youtube-api-quota-limits.md`

Bad: `YouTube_API_Quota.md` (uppercase, underscores, no date)

## Placement

Placement follows the location resolved in `$RESEARCH_SKILL/rules/core.md § Output-Location Resolution`.

| Location | Path | Notes |
|----------|------|-------|
| Standalone | `$PROJECT_FOLDER/research/YYYY-MM-DD-<slug>/YYYY-MM-DD-<topic>.md` | The dated `<slug>` folder holds one or more dossiers and, when applicable, a `YYYY-MM-DD-summary-and-position.md` |
| Work package | `$PROJECT_FOLDER/work/<wp>/research/YYYY-MM-DD-<topic>.md` | Flat, alongside any `YYYY-MM-DD-summary-and-position.md`; no nested campaign folder |

The standalone form always uses a dated folder, even for a single dossier, so a later second dossier or synthesis has a home without restructuring. The work-package form stays flat to match the existing work-package `research/` convention.
