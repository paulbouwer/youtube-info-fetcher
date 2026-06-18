# Implementation Log

## Phase 1 — Author skill scaffold (manifest + rules + templates)

Date: 2026-06-18

### What was done

Authored the `research` skill scaffold through the `agent-system` skill's `author`
action, against `conventions.md`, `skill.md`, `rule.md`, `rules-index.md`, and the
matching templates. Seven files created under `.agents/skills/research/`:

- `SKILL.md` — trigger-first manifest; two capabilities (Create Research, Synthesise
  Research) with `Load when`; lean Rules index-pointer; Bundled Templates table with
  `Load when`. (REQ001, REQ002)
- `rules/core.md` — artifact-first output; read-only research boundary; `single` /
  `focused-swarm` modes (`full-swarm` not selectable); output-location resolution
  (explicit-intent-first, context fallback); focused-swarm capture and synthesis;
  completion criteria. (REQ013)
- `rules/citations.md` — mandatory `[F##]` source index referenced inline; local refs
  as plain paths with line ranges (no `#file:`); external refs as markdown links;
  read-don't-guess; evidence vs inference. (REQ014)
- `rules/dossier.md` — eight required dossier sections; `YYYY-MM-DD-<topic>.md` naming;
  standalone dated-folder vs work-package flat placement. (REQ015)
- `rules/rules.index.md` — file map of the three rules with a `## Non-Overridable Rules`
  section covering `citations.md` and the read-only-boundary portion of `core.md`. (REQ016)
- `templates/research-dossier.md` — single-topic dossier mirroring the dossier rule's
  required sections. (REQ017)
- `templates/summary-position.md` — cross-dossier synthesis (position + opinion +
  Gaps / Next Research; no action/routing recommendations). (REQ018)

### Verification

- Self-validated against `agent-system` `validation.md` (Universal + Skill Manifest +
  Rule + Rules Index + Cross-Cutting groups): no `---` separators outside frontmatter,
  no emojis or arrows in prose, only declared variables used, dossier rule and template
  sections agree.
- AC006 satisfied (skill rules exist; `rules.index.md` marks `citations.md` and the
  read-only boundary non-overridable). AC001-005, AC007, AC008 remain pending their
  later phases (actions, wiring, review).

### Learnings and things to improve

- The scaffold references `$RESEARCH_SKILL`, which is not defined until Phase 4
  (registry wiring in `.agents/AGENTS.md`). This is the conformant choice — paths must
  use the variable — but it means a full `agent-system` review only passes after Phase 4.
  Worth confirming the Phase 5 review accounts for this ordering.
- The cross-cutting rule-template agreement check is clean for the dossier
  (`dossier.md` <-> `research-dossier.md`). The `summary-position.md` template's required
  sections are owned by the `synthesise-research` action (Phase 3) rather than a rule;
  ensure that action enumerates them so the template still traces back to an authority.
- Nested backticks in `Good:` / `Bad:` examples render poorly; prefer prose-with-inline-code
  over wrapping a whole example line in backticks.

## Phase 2 — Author create-research

Date: 2026-06-18

### What was done

Authored `actions/create-research.md` through the `agent-system` `author` action against
`action.md` and the action template. Six goal-prefixed steps:

1. Resolve Output Location — explicit-intent-first, context-detection fallback. (REQ006)
2. Scope the Research and Select Mode — `single` default, `focused-swarm` for user-described
   lenses; `full-swarm` not offered; `grill-me` referenced for ambiguity. (REQ007, REQ009)
3. Gather Evidence — code, docs, and web sources; cited per `citations.md`. (REQ008)
4. Synthesise Findings into One Dossier — focused-swarm capture and synthesis.
5. Write the Dossier — fill the template, naming and placement per `dossier.md`. (REQ005)
6. Stop at the Read-Only Boundary — no implementation edits, no routing recommendations. (REQ005)

An Error Handling table covers ambiguous scope/location, `full-swarm` requests, unreadable
sources, implementation-edit requests, and filename conflicts.

### Verification

- Self-validated against `agent-system` `validation.md` (Universal + Action groups): only
  declared variables, no `---` separators, no emojis/arrows, every step carries a `**Goal:**`,
  Error Handling uses the `| Scenario | Action |` table.
- AC003 satisfied (`create-research` supports `single` and `focused-swarm`; `full-swarm` not
  selectable). AC007 (both actions reference `grill-me`) is partially met — `create-research`
  references it; pending `synthesise-research` in Phase 3.

### Learnings and things to improve

- `grill-me` is a vendored skill with no registered variable in `.agents/AGENTS.md` and no
  Skill Catalog row. It is therefore referenced by name (the `grill-me` skill), not by a
  `$VAR` (which would be undeclared) or a bare path. Worth deciding later whether `grill-me`
  should be registered for consistency.
