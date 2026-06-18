---
related: https://github.com/paulbouwer/youtube-info-fetcher/issues/6
---

## Approach

Author the new `research` skill **through the `agent-system` skill** end-to-end, so the output is
aligned with the agent system by construction rather than by hand. Use the `agent-system` `author`
action and its governing rules to author each artefact against the rule that defines its shape —
`skill.md` for `SKILL.md`, `action.md` for the two actions, `rule.md` for the three rules,
`rules-index.md` for `rules.index.md`, and `conventions.md` plus the `skill`/`action`/`rule`
templates throughout. Then close with the `agent-system` `review` action (its `validation.md` +
`checklist.md`) as the alignment gate. Build the strict core first (manifest + rules + templates),
then the two actions, then wire the registry, then validate.

The skill is **small at the surface, strict at the core**: a minimalist trigger-first manifest,
invariants (citation integrity, read-only boundary, durable artifact, dual-location resolution)
in immutable skill rules, and the dossier/synthesis shapes in templates rather than the manifest.
Scope is deliberately reduced from the source position: two actions (`create-research`,
`synthesise-research`), no `prepare-handoff`, and `full-swarm` deferred.

Source position: `project/research/2026-05-31-research-capability-design/2026-05-31-summary-and-position.md`.

## Phases

### Phase 1 — Author skill scaffold (manifest + rules + templates)

Author every file below via the `agent-system` `author` action against its governing rule
(`skill.md` for `SKILL.md`, `rule.md` for each rule, `rules-index.md` for `rules.index.md`,
`conventions.md` + templates throughout).

- [ ] Create `.agents/skills/research/SKILL.md` (minimalist, trigger-first; Capabilities table
      with `Load when`; lean `Rules` index-pointer; Bundled Templates table with `Load when`;
      lazy Flow). Define skill Variables block. (REQ001, REQ002)
- [ ] Create `.agents/skills/research/rules/core.md` — artifact-first; read-only boundary; modes
      (`single`/`focused-swarm`); output-location resolution (explicit-intent-first, context
      fallback; standalone dated folder vs work-package flat `research/`); focused-swarm capture
      + synthesis; completion criteria. (REQ013)
- [ ] Create `.agents/skills/research/rules/citations.md` — `[F##]` source index; local refs as
      plain paths with line ranges; external refs as markdown links; read-don't-guess; separate
      evidence from inference. (REQ014)
- [ ] Create `.agents/skills/research/rules/dossier.md` — required sections; `YYYY-MM-DD-<topic>.md`
      naming; standalone-folder vs work-package placement. (REQ015)
- [ ] Create `.agents/skills/research/rules/rules.index.md` — file map listing the three rules,
      with a `## Non-Overridable Rules` section covering `citations.md` and the read-only-boundary
      portion of `core.md`. (REQ016)
- [ ] Create `.agents/skills/research/templates/research-dossier.md` (single-topic dossier). (REQ017)
- [ ] Create `.agents/skills/research/templates/summary-position.md` (cross-dossier synthesis). (REQ018)

### Phase 2 — Author `create-research`

Author via the `agent-system` `author` action against `action.md` + the `action` template.

- [ ] Create `.agents/skills/research/actions/create-research.md`: location resolution
      (explicit-intent-first + context fallback), modes (`single` default, `focused-swarm` with
      user-described lenses; `full-swarm` not exposed), evidence sources (code/docs/web), template
      fill, read-only stop boundary, and `grill-me` reference for ambiguity.
      (REQ005, REQ006, REQ007, REQ008, REQ009)

### Phase 3 — Author `synthesise-research`

Author via the `agent-system` `author` action against `action.md` + the `action` template.

- [ ] Create `.agents/skills/research/actions/synthesise-research.md`: consume one or more
      dossiers; produce a `summary-position` document with position + opinion + "Gaps / Next
      Research"; no action/routing recommendations; reference `grill-me` before forming a position.
      (REQ010, REQ011, REQ012)

### Phase 4 — Wire the registry

- [ ] Add `RESEARCH_SKILL := $AGENT_SYSTEM_FOLDER/skills/research` to `.agents/AGENTS.md § Variables`. (REQ003)
- [ ] Add a Skill Catalog registry row (keywords: `research, investigate, dossier, evidence,
      sources, synthesise`; actions: `create-research, synthesise-research`). (REQ003)
- [ ] Note research outputs in the root `AGENTS.md § Project System` documentation map. (REQ004)

### Phase 5 — Validate

- [ ] Run the `agent-system` review action against the bundled checklist; resolve findings. (REQ019)
- [ ] Dry-run both location modes (standalone, work-package) and both research modes
      (`single`, `focused-swarm`). (REQ020)
- [ ] Author `verification-results.md` mapping each issue AC to Pass/Fail with evidence.

## Verification

- Each acceptance criterion in issue #6 is satisfied and recorded in `verification-results.md`.
- `grep` confirms no `### Bundled Rules` eager-load and that `rules.index.md` contains a
  `## Non-Overridable Rules` section naming `citations.md` and the read-only boundary.
- The `agent-system` review checklist passes for the new skill.

## Decisions

Resolved during planning (grilling session, 2026-06-18) against the source position document:

| Decision | Resolution |
|---|---|
| Action set | Two actions: `create-research`, `synthesise-research`. `prepare-handoff` dropped. |
| Next-steps concern | Research is fully standalone; artifacts make no action/routing recommendations. The user decides next steps. |
| Synthesis forward-looking content | Position + opinion + "Gaps / Next Research"; no action/routing recommendations. |
| Research modes | `single` (default) + `focused-swarm` (user describes lenses). `full-swarm` deferred, not selectable. |
| Output location | Explicit user intent first (name a work package or "standalone"); context detection as fallback. |
| Rules | Three: `core.md`, `citations.md`, `dossier.md`. `citations.md` + read-only boundary non-overridable. |
| Templates | Two: `research-dossier.md`, `summary-position.md`. |
| Evidence sources | Code, docs, and web; external as markdown links, local as plain paths with line ranges. |
| `grill-me` composition | Referenced (not duplicated) from BOTH actions. |
| Project-rules tier | Deferred; not added unless a real deviation appears. |
| Synthesis action name | `synthesise-research.md` (British spelling, matches repo conventions). |
| Synthesis input | One or more dossiers (works on a single dossier too). |
| Registry wiring | `RESEARCH_SKILL` variable + registry row + note in root `AGENTS.md` Project System. |
| Decision recording | Resolved decisions captured here; source research artifact left unchanged; `future-enhancement-ideas.md` untouched. |
