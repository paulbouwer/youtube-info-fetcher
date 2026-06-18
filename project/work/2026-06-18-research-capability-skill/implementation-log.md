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
