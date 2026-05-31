---
related: https://github.com/paulbouwer/youtube-info-fetcher/issues/4
---

## Approach

Correct the SKILL.md routing layer so it honours the lazy-loading philosophy already
declared in `.agents/AGENTS.md`. Treat this as an error correction, not a doctrine change.

Work in two stages: first codify the corrected pattern in the `agent-system` authoring
authority (so newly authored skills inherit it), then reflow every existing routing
manifest and its actions to conform. Finish by validating with the updated
`rules/validation.md` checks and grep-based assertions.

The corrected SKILL.md pattern is:

- Capabilities table gains a `Load when` column (intent trigger per capability).
- `### Bundled Rules` enumeration → lean `### Rules` index-pointer + "load only the rules
  whose `Load when` matches the selected capability".
- `Bundled Templates` table gains a `Load when` column (skills with templates only).
- Flow routes via `Load when`, then loads only capability-matched rules, then
  capability-matched templates, then executes.
- Action `### Prerequisites` say the rules *relevant to this capability* (and templates,
  where applicable) were loaded by `SKILL.md`.

## Phases

### Phase 1 — Codify the corrected pattern in the agent-system authority

- [x] `rules/skill.md` — rewrite Purpose, Configuration-tables spec (Capabilities `Load when`; `Rules` index-pointer replacing `Bundled Rules`; `Bundled Templates` `Load when`), and the Flow Pattern (lazy ordering). Reframe the Config-First section to support route-then-load.
- [x] `templates/skill.md` — update Configuration tables (`Load when` columns; lean `Rules` pointer) and Execution Steps to the lazy ordering.
- [x] `rules/action.md` + `templates/action.md` — reword the precondition to capability-scoped ("relevant to this capability"; keep "and templates" only where the skill bundles templates).
- [x] `rules/validation.md` — remove the "Bundled Rules table required" and "load bundled rules before templates before routing" checks; add checks for `Load when` on Capabilities (and Bundled Templates), the lean `Rules` index-pointer, lazy Flow ordering, and capability-scoped action preconditions.
- [x] Confirm `rules/checklist.md` needs no change.

### Phase 2 — Reflow the routing manifests (5 SKILL.md)

- [x] `agent-system/SKILL.md` — Capabilities `Load when`; `Rules` index-pointer (Skill Rules Index only); `Bundled Templates` `Load when`; lazy Flow.
- [x] `decision-records/SKILL.md` — same; retain Index Maintenance post-step.
- [x] `devcontainer/SKILL.md` — same; retain the "actions load project rules by detected context" sentence.
- [x] `git-workflow/SKILL.md` — Capabilities `Load when`; `Rules` index-pointer with Skill + Project Rules Index rows; lazy Flow (no templates).
- [x] `work-tracking/SKILL.md` — same as git-workflow; preserve the remote-issue/board prerequisites.

### Phase 3 — Reword action preconditions (15)

- [x] agent-system: `author.md`, `review.md`.
- [x] decision-records: `create.md`, `review.md`.
- [x] devcontainer: `create.md`, `review.md`.
- [x] git-workflow: `commit.md`, `create-branch.md`, `create-pull-request.md`.
- [x] work-tracking: `create-issue.md`, `create-work-package.md`, `update-issue.md`, `update-work-package.md`.

### Phase 4 — Validate

- [x] Run the updated `rules/validation.md` checks against all five reflowed manifests and their actions.
- [x] Grep assertions: no `### Bundled Rules` heading in any routing manifest; no Execution Step that loads bundled rules before routing; no action precondition containing "all bundled rules"; every Capabilities table contains a `Load when` column.
- [x] Record results in `verification-results.md`.

## Verification

- Every Success Criterion in `spec.md` is demonstrably met (grep + manual review).
- `rules/validation.md` passes against the reflowed assets.
- A spot-check confirms a new skill authored from `templates/skill.md` produces the lazy
  pattern by default.

## Decisions

- **Error correction, not a doctrine change.** `AGENTS.md` already declares lazy loading;
  the eager SKILL.md layer contradicted it. No decision record is authored.
- **No `learnings.md` entry.** The learning is codified directly into the agent-system
  skill rules/templates (its permanent home); `learnings.md` is a staging area for
  not-yet-codified learnings.
- **Standardise, do not copy verbatim.** The reference repo only fully migrated its
  work-package actions; this work standardises the capability-scoped precondition wording
  across **all** actions.
- **`grill-me` exempt; `rules.index.md` and `AGENTS.md` catalog unchanged** — already
  lazy / already match the reference.
- **Project Rules Index row** appears only for skills with a 1:1 project-rules domain
  (`git-workflow`, `work-tracking`); other skills list the Skill Rules Index only and keep
  their context-based project-rules loading sentence.
- **Anonymised artefacts.** No source repository name or path appears in any work-package
  artefact (per the goal conditions).
