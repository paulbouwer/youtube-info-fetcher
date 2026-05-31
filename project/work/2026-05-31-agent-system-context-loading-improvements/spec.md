## Problem

This repo's agent system declares a lazy-loading philosophy in `.agents/AGENTS.md`
(*"Lazy Loading — Load only the skills and rules relevant to the current task"*), but the
SKILL.md manifest layer does not honour it. Every substantive `SKILL.md` enumerates a
`### Bundled Rules` table and instructs the agent to *"Load the bundled rules … into
context"* **before** routing intent — eager-loading every bundled rule for every
capability, including rules the selected capability will never use. The `agent-system`
skill actively codifies this eager behaviour in its authoring authority (`rules/skill.md`,
`templates/skill.md`, `rules/validation.md`) and in each action's precondition
(*"assumes all bundled rules … loaded"*).

This is an **implementation error** — a drift from the always-intended lazy-loading
design — not a change of direction. A separate reference repository had already carried
the lazy-loading refinement further; this work brings that learning in to correct the
SKILL.md layer so it matches the declared philosophy and improves context efficiency.

## Requirements

### Correct the SKILL.md routing pattern (lazy)

- Each routing `SKILL.md` Capabilities table MUST carry a **`Load when`** column giving a
  concrete intent trigger per capability.
- The `### Bundled Rules` enumeration MUST be replaced by a lean `### Rules` index-pointer
  table (Skill Rules Index; plus a Project Rules Index row only where the skill maps 1:1
  to a project-rules domain) followed by the instruction to *consult each index and load
  only the rule files whose `Load when` matches the selected capability*.
- Where a skill bundles skill-level templates, the `Bundled Templates` table MUST carry a
  **`Load when`** column.
- The Flow Execution Steps MUST route first (select capability via `Load when`), then load
  only the capability-matched rules, then the capability-matched templates, then execute —
  never eager-load all bundled rules up front.

### Correct the action preconditions (capability-scoped)

- Every action's `### Prerequisites` precondition MUST state that the rules *relevant to
  this capability* (and templates, for skills that bundle them) have been loaded by
  `SKILL.md` — not *all bundled* rules.
- Actions MUST continue to never self-load bundled rules; the skill performs the lazy
  selection.

### Codify the correction in the agent-system authority

- `rules/skill.md`, `templates/skill.md`, `rules/action.md`, `templates/action.md`, and
  `rules/validation.md` MUST be updated so the lazy pattern is the authored standard and
  validation no longer requires the eager structure.
- `rules/validation.md` MUST validate the corrected structure: `Load when` on Capabilities
  (and Bundled Templates), the lean `Rules` index-pointer, lazy Flow ordering, and
  capability-scoped action preconditions.

### Apply repo-wide

- Reflow all five routing manifests: `agent-system`, `decision-records`, `devcontainer`,
  `git-workflow`, `work-tracking`, plus all their action files.

## Out of Scope

- `grill-me` (interactive skill; no Configuration/Capabilities routing manifest).
- The `rules.index.md` two-level lazy index layer (already correct, already carries
  `Load when`).
- `AGENTS.md` skill catalog (already keyword-routed; matches the reference).
- `rules/checklist.md` (encodes no loading doctrine).
- A decision record (this is an error correction, not a new/reversed decision) and a
  `learnings.md` entry (the learning is codified directly into the agent-system skill).
- Any source-repository names or paths in work-package artefacts.

## Success Criteria

- No routing `SKILL.md` contains a `### Bundled Rules` enumeration or an Execution Step
  that loads bundled rules before routing.
- Every routing `SKILL.md` Capabilities table has a `Load when` column with concrete
  trigger phrases; every Bundled Templates table (where present) has a `Load when` column.
- Every routing `SKILL.md` has a lean `### Rules` index-pointer with the lazy-load
  instruction.
- No action `### Prerequisites` says *"all bundled rules"*; all say the
  capability-relevant rules (and templates where applicable) were loaded by `SKILL.md`.
- `rules/validation.md` checks the corrected structure and passes when run against all five
  reflowed manifests and their actions.
- The agent-system template and rule for SKILL.md and actions reflect the lazy pattern, so
  a newly authored skill inherits it by default.
