# Research Core Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$RESEARCH_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Core process rules for research: what the output is, where the work stops, how it decomposes, where it lands, and when it is done.

| Item | Rule |
|------|------|
| **Artifact-first** | The durable artifact is the canonical output, not the conversation |
| **Read-only boundary** | Research reads sources and writes research artifacts only — never implementation edits (non-overridable) |
| **Modes** | `single` (default) or `focused-swarm` (user-described lenses); `full-swarm` is deferred and not selectable |
| **Location resolution** | Explicit user intent first; context detection as fallback |
| **Standalone placement** | Dated folder `$PROJECT_FOLDER/research/YYYY-MM-DD-<slug>/` |
| **Work-package placement** | Flat `$PROJECT_FOLDER/work/<wp>/research/` |
| **Completion** | Enough cited evidence to act, with the dossier's required sections filled |

## Artifact-First Output

The canonical output of research is the durable artifact — a dossier (see `$RESEARCH_SKILL/rules/dossier.md`) or a summary and position document — not the chat transcript. Findings that live only in conversation are lost; every claim worth keeping belongs in the artifact, with a citation (see `$RESEARCH_SKILL/rules/citations.md`).

A research artifact is stand-alone. It makes no action or routing recommendations and does not direct the reader into other skills. The user decides what to do with it, immediately or later.

## Read-Only Research Boundary

Research operates within a read-only boundary. While researching, the only files written are the research artifacts themselves (the dossier and, for synthesis, the summary and position document).

- **MUST** read freely from code, docs, and web sources to gather evidence.
- **MUST NOT** make implementation edits — no changes to source, configuration, build, or other non-research files during research.
- **MUST** stop once the artifact is complete. The user decides the next step.

This boundary is non-overridable (see `$RESEARCH_SKILL/rules/rules.index.md § Non-Overridable Rules`).

## Research Modes

Research decomposes into one of two modes. Parallelism is opt-in, never the default.

| Mode | When | Output |
|------|------|--------|
| `single` | Default. The topic is a single coherent line of investigation | One dossier authored directly |
| `focused-swarm` | The user describes a small set of independent lenses or views worth researching separately | One dossier synthesised from the per-lens findings |

`full-swarm` (a fixed roster of typed worker lenses) is deferred. It **MUST NOT** be exposed or offered as a selectable mode until its worker roles, source capture, and synthesis rules are defined.

### Focused-Swarm Capture and Synthesis

In `focused-swarm`, the user names the lenses — each lens is an independent view of the topic (for example, a distinct source family or analytical angle).

- Each lens is researched independently and its findings captured with citations per `$RESEARCH_SKILL/rules/citations.md`.
- The per-lens findings are synthesised into **one** dossier — `focused-swarm` never emits multiple competing artifacts.
- The combined source index in the final dossier carries every reference used across all lenses, deduplicated.

## Output-Location Resolution

The output location is resolved by intent first, then by context.

1. **Explicit user intent first.** If the user names a target work package, or asks for standalone research, honour that choice.
2. **Context detection as fallback.** When intent is not stated:
   - Inside or linked to a `$PROJECT_FOLDER/work/<wp>` context, use the work-package path.
   - Otherwise, use a standalone dated folder.

| Mode | Path | Notes |
|------|------|-------|
| Standalone | `$PROJECT_FOLDER/research/YYYY-MM-DD-<slug>/` | Always a dated folder, even for a single dossier, so a later second dossier or synthesis has a home without restructuring |
| Work package | `$PROJECT_FOLDER/work/<wp>/research/` | Flat, matching the existing work-package convention — no nested campaign folder |

The dossier content is identical in both locations; only the resolved path differs. Naming and placement detail live in `$RESEARCH_SKILL/rules/dossier.md`.

When scope or the target location is ambiguous and the answer is not in the sources, resolve it by interrogation rather than guessing — see the `grill-me` skill, referenced (not duplicated) by the actions.

## Completion Criteria

Research is complete when there is enough cited evidence to act on the topic, and:

- The dossier's required sections are filled (see `$RESEARCH_SKILL/rules/dossier.md`).
- Every claim is backed by a citation (see `$RESEARCH_SKILL/rules/citations.md`).
- Direct evidence is separated from inference.

Completion is about sufficiency of evidence, not exhaustiveness. Remaining unknowns are recorded in the dossier's Gaps / Next Research section rather than blocking the artifact.
