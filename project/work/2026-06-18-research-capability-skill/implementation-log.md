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

## Phase 3 — Author synthesise-research

Date: 2026-06-18

### What was done

Authored `actions/synthesise-research.md` through the `agent-system` `author` action
against `action.md` and the action template. Five goal-prefixed steps:

1. Collect the Dossiers — one or more inputs; resolve the position-document location. (REQ010)
2. Read and Compare the Dossiers — read in full; build a consolidated, deduplicated `[F##]`
   index; separate evidence from inference.
3. Interrogate Before Forming a Position — pressure-test via the `grill-me` skill. (REQ012)
4. Write the Summary and Position — fill the template (position + opinion + Gaps / Next
   Research); no action/routing recommendations. (REQ010, REQ011)
5. Stop at the Read-Only Boundary — no implementation edits.

The Step 4 section list enumerates every `summary-position.md` template section, closing the
Phase 1 traceability gap (the template now traces back to this action's authority).

### Verification

- Self-validated against `agent-system` `validation.md` (Universal + Action groups): only
  declared variables, no `---` separators, no emojis/arrows, every step carries a `**Goal:**`,
  Error Handling uses the `| Scenario | Action |` table.
- AC004 satisfied (`synthesise-research` consumes one or more dossiers; emits position +
  opinion + Gaps / Next Research; no action/routing recommendations).
- AC007 satisfied (both `create-research` and `synthesise-research` reference the `grill-me`
  skill).

### Learnings and things to improve

- Both actions now share the same `grill-me` reference pattern (`$GRILL_ME_SKILL`) and the
  same read-only-boundary close-out step. If a third action is ever added, this repetition
  would be worth factoring into a shared rule section rather than restating per action.

## Phase 4 — Wire the registry

Date: 2026-06-18

### What was done

- Defined `RESEARCH_SKILL := $AGENT_SYSTEM_FOLDER/skills/research` in
  `.agents/AGENTS.md § Variables`. (REQ003)
- Added a Skill Catalog registry row: Research / keywords `research, investigate, dossier,
  evidence, sources, synthesise` / actions `create-research, synthesise-research`. (REQ003)
- Noted research outputs in the root `AGENTS.md § Project System` map — added a `research/`
  entry to the directory tree and a Research bullet describing standalone and work-package
  placement. (REQ004)

### Verification

- All seven skill variables are defined and each has a matching Catalog row (no orphans); the
  Catalog references only defined variables.
- Every `$RESEARCH_SKILL` reference across the seven research skill files now resolves.
- AC008 partially satisfied: `RESEARCH_SKILL` and the registry row exist. The remaining
  clause (passes the `agent-system` review checklist) is verified in Phase 5.

### Learnings and things to improve

- Variable and Catalog ordering were kept consistent (Work Tracking, Research, Grill Me) in
  both the Variables block and the registry, so a reader can scan either in the same order.
- The pre-existing `Grill Me` Catalog row uses padded-cell alignment that differs from the
  other rows; left as-is to avoid unrelated churn, but the table style is mixed.
