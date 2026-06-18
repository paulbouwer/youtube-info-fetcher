## Problem

The repo has no first-class way to produce durable, citation-backed research. Investigation
happens ad hoc in chat and is lost, with no consistent artifact, no citation discipline, and
no defined home for the output. A prior research effort
(`project/research/2026-05-31-research-capability-design/`) analysed three mature reference
implementations and landed a position: build a lean, structured `research` skill, dual-location
by design, with citation integrity as an immutable rule and parallel "swarm" research as an
opt-in mode rather than the default.

This work package implements a scoped subset of that position. It delivers the two core
capabilities — **create research** and **synthesise a position** — and explicitly excludes the
proposed `prepare-handoff` capability. The skill must work standalone (landing under
`project/research/`) and as part of a work package (landing under `project/work/<wp>/research/`),
chosen flexibly. Research artifacts are stand-alone outputs that make no action or routing
recommendations; the user decides what to do with them, immediately or later.

## Requirements

Requirements use stable `REQ###` ids so the plan phases and verification can reference them
directly.

All artefacts in this work package are authored and validated **through the `agent-system`
skill** so they conform to the agent system by construction: the `author` action and its
governing rules (`conventions.md`, `skill.md`, `action.md`, `rule.md`, `rules-index.md`, and the
`skill`/`action`/`rule` templates) drive authoring; the `review` action and its
`validation.md`/`checklist.md` gate the result. This alignment is itself a requirement, expressed
per artefact in REQ001 (skill/manifest), REQ013–REQ016 (rules), REQ017–REQ018 (templates), and
REQ005/REQ010 (actions), and enforced at REQ019.

### Skill shape and wiring

- **REQ001**: A new `research` skill at `.agents/skills/research/` authored via the `agent-system`
  skill's `author` action, conforming to its rules — `conventions.md`, `skill.md` (for `SKILL.md`),
  `action.md` (for actions), `rule.md` (for rules), `rules-index.md` (for `rules.index.md`) — and
  the `skill`/`action`/`rule` templates (Variables blocks, no emojis, no `---` horizontal rules).
- **REQ002**: `SKILL.md` manifest is minimalist and trigger-first, routing to the two actions via
  a Capabilities table with a `Load when` column and a lean `Rules` index-pointer (lazy loading).
- **REQ003**: Register the skill — define `RESEARCH_SKILL := $AGENT_SYSTEM_FOLDER/skills/research`
  in `.agents/AGENTS.md § Variables` and add a registry row (keywords: `research, investigate,
  dossier, evidence, sources, synthesise`; actions: `create-research, synthesise-research`).
- **REQ004**: Note research outputs in the root `AGENTS.md § Project System` documentation map.

### Actions (two only)

- **REQ005**: `create-research.md` resolves output location, runs research, and writes **one**
  cited dossier from the `research-dossier` template, stopping at the read-only boundary.
- **REQ006**: `create-research` output-location resolution is **explicit user intent first** (user
  may name a target work package or request "standalone") with **context detection as fallback**
  (inside or linked to a `project/work/<wp>` → work-package path; otherwise a standalone dated
  folder).
- **REQ007**: `create-research` supports modes `single` (default) and `focused-swarm` (the user
  describes the lenses / independent views to run, synthesised into one dossier). `full-swarm` is
  deferred and MUST NOT be exposed as a selectable mode.
- **REQ008**: `create-research` supports evidence sources code, docs, and web.
- **REQ009**: `create-research` references (does not duplicate) the `grill-me` skill when scope or
  requirements are ambiguous and the answer is not in the sources.
- **REQ010**: `synthesise-research.md` consumes **one or more** dossiers and produces a summary +
  position document from the `summary-position` template.
- **REQ011**: `synthesise-research` output is a position and opinion plus a "Gaps / Next Research"
  section, and MUST NOT emit action or routing recommendations (no handoff into other skills).
- **REQ012**: `synthesise-research` references (does not duplicate) the `grill-me` skill before
  forming a position.

### Rules (three; strict core)

- **REQ013**: `core.md` enforces artifact-first output; the **read-only research boundary** (no
  implementation edits during research); decomposition modes (`single`/`focused-swarm`) and when
  each applies; output-location resolution (explicit-intent-first, context fallback; standalone
  dated folder vs work-package flat `research/`); how `focused-swarm` lens findings are captured,
  cited, and synthesised into one dossier; and completion criteria (enough evidence to act).
- **REQ014**: `citations.md` enforces a mandatory `[F##]` source index referenced inline; local
  workspace refs as plain paths (with line ranges; no `#file:` directives), external refs as
  markdown links; "read sources, do not guess"; and separation of direct evidence from inference.
- **REQ015**: `dossier.md` enforces required dossier sections, naming `YYYY-MM-DD-<topic>.md`, and
  standalone-folder vs work-package placement conventions.
- **REQ016**: `rules.index.md` is a file-map index that lists `citations.md` and the
  read-only-boundary portion of `core.md` under a `## Non-Overridable Rules` section; location
  policy and cross-reference conventions remain overridable.

### Templates (two)

- **REQ017**: `research-dossier.md` — single-topic dossier, identical content in both locations.
- **REQ018**: `summary-position.md` — cross-dossier synthesis (position + opinion + gaps/next
  research).

### Validation

- **REQ019**: The skill passes the `agent-system` skill's `review` action against its
  `validation.md` and bundled `checklist.md`; any findings are resolved.
- **REQ020**: Both location modes (standalone and work-package) and both research modes (`single`,
  `focused-swarm`) are dry-run validated.

## Out of Scope

- `prepare-handoff` capability and any automated routing of research into `work-tracking` or
  `decision-records`. Research artifacts make no action/routing recommendations.
- The typed `full-swarm` (8-subagent) mode — deferred; not exposed as selectable.
- Workshop artifacts and external-research-prompt generators.
- A mutable project-rules tier at `.agents/rules/research/` — not added unless a real deviation
  appears; invariants live in the skill rules.
- Modifying the source research artifact under
  `project/research/2026-05-31-research-capability-design/` (left unchanged).
- Recording deferred items in `project/future-enhancement-ideas.md` (this Out of Scope section
  is the record).

## Success Criteria

- Running research standalone produces a dated, `[F##]`-cited dossier under
  `project/research/<dated-slug>/` from the `research-dossier` template.
- The same skill invoked for a work package writes identical dossier content (flat) under
  `project/work/<wp>/research/`.
- `create-research` supports `single` (default) and `focused-swarm` (user-described lenses);
  `full-swarm` is not selectable.
- `synthesise-research` consumes one or more dossiers and emits a `summary-position` document
  with a position, opinion, and "Gaps / Next Research" — and no action/routing recommendations.
- Every dossier carries a mandatory `[F##]` source index; local refs are plain paths with line
  ranges, external refs are markdown links; research stops at the read-only boundary.
- `rules.index.md` marks `citations.md` and the read-only boundary as `## Non-Overridable Rules`.
- Both actions reference (do not duplicate) `grill-me`.
- `RESEARCH_SKILL` and the registry row exist in `.agents/AGENTS.md`; the skill passes the
  `agent-system` review checklist.
