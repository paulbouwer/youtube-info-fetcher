# Summary and Position: A Research Capability for the Agent System

| Field | Value |
|---|---|
| Generated date | 2026-05-31 |
| Research Query | How should this repo add a research capability that works both standalone (`project/research/`) and inside a work package (`project/work/<wp>/research/`), and fits the existing skills/rules/actions/templates agent system? |
| Mode | Standalone research (synthesis) |
| Scope | Synthesises three sibling reference dossiers in this folder and positions a concrete design against this repo's agent-system conventions |
| Method | Cross-reading the three reference dossiers, mapping their convergent patterns onto this repo's `.agents` skill/action/rule/template model and its work-package conventions |
| Inputs | `[D1]` jakkaj/tools, `[D2]` mattpocock/skills, `[D3]` microsoft/hve-core (see Sources) |

## 1. Executive Summary

### What this answers

Whether — and how — to build a first-class **research capability** as an
agent-system skill, given three mature reference implementations and this repo's
existing conventions. The deliverable is an opinion and an actionable plan, not
an implementation.

### Headline position

**BUILD IT — as a lean, structured `research` skill, dual-location by design,
with citation discipline as an immutable rule, and parallel "swarm" research as
an opt-in mode rather than the default.** All three references independently
converge on the same load-bearing ideas; this repo's conventions already supply
the folder, rule, and template machinery to express them cleanly. The main risk
is over-building (jakkaj's 8-subagent swarm, workshops, external-research
prompts all at once); the mitigation is to ship a small core first and grow.

### Key insights

1. **Two strong models for artifact-first cited research, plus one for skill
   design.** hve-core `[D3]` and jakkaj `[D1]` strongly support the same
   blueprint: a durable, cited research artifact; optional parallel decomposition
   synthesised into one document; a read-only research boundary; and an
   artifact-first handoff into planning. mattpocock `[D2]` is deliberately more
   mixed — research is usually embedded inside task skills, some outputs are
   ephemeral (OS temp), and there is no general citation protocol — so its
   contribution is different: minimalist skill design, interrogation (`grill-me`),
   feedback-loop discipline (`diagnose`), and selective parallelism. The two
   blueprints converging independently is strong signal; mattpocock sharpens the
   *packaging*, not the artifact contract.
2. **Artifact-first, not chat-first.** hve-core's most reusable idea is that the
   research *document* is the canonical output consumed by Plan — Plan starts by
   locating research, checking coverage, filling gaps, and recording deviations
   rather than improvising from conversation `[D3]`. This is the single biggest
   reliability lever and should be a core rule.
3. **Citation integrity is a rule, not a suggestion.** hve-core treats evidence
   (paths + line ranges, external URLs, evaluated alternatives) as a completion
   criterion `[D3]`; jakkaj is strong on file references but inconsistent on a
   final source index `[D2 -> D1]`; mattpocock mostly leaves it implicit `[D2]`.
   The target should be stricter than all three: a mandatory `[F##]` source index
   plus "read files, do not guess".
4. **Decomposition is valuable but should be opt-in.** jakkaj's broad codebase
   dossier launches 8 typed subagents (IA/DC/PS/QT/IC/DE/PL/DB) `[D1]`; hve-core
   parallelises independent research questions `[D3]`; mattpocock reserves
   parallelism for genuinely independent axes (`review`, `design-an-interface`)
   `[D2]`. The lesson: parallelise only when research decomposes into independent
   source families or analytical lenses, and always synthesise into one artifact.
5. **Minimalism for the manifest; structure for the invariants.** mattpocock's
   house style — small, composable, trigger-first descriptions — is the right
   model for `SKILL.md` `[D2]`. But a citation-backed capability has enough
   invariant process and templated output to justify splitting actions, rules,
   and templates out of the manifest (which this repo's model already mandates).
6. **Dual-location is a solved problem.** Both jakkaj (plan-folder detection) and
   hve-core (dated tracking folders) detect output context `[D1][D3]`. Adapt the
   mechanism — not the paths — to this repo's `project/research/<dated-slug>/`
   (standalone) and `project/work/<wp>/research/` (work-package) convention.
7. **Reuse what already exists.** This repo already vendors `grill-me` (from
   mattpocock) `[D2]`. The research skill should *compose* it for ambiguity
   resolution rather than reinvent interrogation, and should hand findings off to
   the existing `work-tracking` and `decision-records` skills rather than absorb
   their jobs.
8. **Dossier vs workshop is a real distinction — but defer the workshop.**
   jakkaj separates broad "what exists / what risks" dossiers from focused "what
   design should we keep open" workshops `[D1]`. Worth adopting eventually; not
   needed for v1. Ship the dossier; record the workshop as a future enhancement.

### Quick stats

| Metric | Value |
|---|---|
| Reference implementations analysed | 3 (`[D1]` `[D2]` `[D3]`) |
| Convergent core patterns | 4 (durable cited artifact; opt-in parallel synthesis; read-only boundary; artifact-first handoff) |
| Proposed v1 actions | 3 (`create-research`, `synthesize-research`, `prepare-handoff`) |
| Proposed v1 research modes | 2 (`single` default, `focused-swarm`); `full-swarm` deferred |
| Proposed skill rules | 3 (`core`, `citations`, `dossier`) + `rules.index.md`; `citations` and the read-only boundary marked non-overridable |
| Proposed templates | 2 (`research-dossier`, `summary-position`) |
| New skill variable | `RESEARCH_SKILL := $AGENT_SYSTEM_FOLDER/skills/research` |
| Existing skills composed | 2-3 (`grill-me`, `work-tracking`, `decision-records`) |
| Deferred to future enhancements | workshops, external-research prompts, full 8-agent swarm |

## 2. Cross-Reference Comparison

| Dimension | jakkaj/tools `[D1]` | mattpocock/skills `[D2]` | hve-core RPI `[D3]` | Position for this repo |
|---|---|---|---|---|
| Packaging | Installable but monolithic `SKILL.md` | Minimalist one-file prompt-skills | Older prompt + agent + subagent + instructions | Structured skill: `SKILL.md` + `actions/` + `rules/` + `templates/` + `rules.index.md` |
| Manifest style | Verbose embedded process | Trigger-first, short, composable | Prompt frontmatter + agent persona | Adopt mattpocock minimalism for the manifest |
| Decomposition | 8 typed subagents (default swarm) | Rare; only for independent axes | Parallel subagents for independent questions | Opt-in modes: `single` / `focused-swarm` / `full-swarm` |
| Output artifact | `research-dossier.md` (+ workshops, external) | Mixed: issues, docs, OS temp, local md | Dated research doc (+ subagent files) | One durable, cited dossier per topic |
| Storage | `docs/plans/<ordinal>-<slug>/` | Mixed / sometimes ephemeral | `.copilot-tracking/research/<date>/` | `project/research/<dated-slug>/` or `project/work/<wp>/research/` |
| Citations | File + file:line; inconsistent index | Mostly implicit | Evidence log as completion criterion | Mandatory `[F##]` source index + read-don't-guess (strictest) |
| Stop boundary | Stops after writing; waits | Per-skill | Research-only; no impl edits | Read-only research boundary as a rule |
| Handoff | Dossier consumed by spec/plan skills | `to-prd` / `to-issues` synthesis | Artifact-first Research -> Plan | `prepare-handoff` recommends and routes to work-tracking / decision-records |
| Interrogation | Embedded in prompts | `grill-me` / `grill-with-docs` | In-context scoping | Compose the existing `grill-me` skill |
| Design research | Workshop artifact | `improve-codebase-architecture` report | Design-thinking overlay (optional) | Defer (future enhancement) |

## 3. Position and Opinion

### The opinion in one paragraph

Build a `research` skill that is **small at the surface and strict at the core**.
The manifest and action files stay short and composable (mattpocock's lesson);
the non-negotiables — citation integrity, evidence verification, durable
artifact, read-only boundary, dual-location resolution — live in immutable skill
rules (hve-core's lesson); the dossier shape lives in a template, not the
manifest (jakkaj's monolith is the anti-pattern). Parallel "swarm" research is a
documented *mode*, not the default, because most research does not decompose into
eight independent lenses and a default swarm is expensive and noisy. The skill
*composes* existing capabilities — `grill-me` for ambiguity, `work-tracking` and
`decision-records` for handoff — rather than re-implementing them. This is the
minimum that captures the convergent value of all three references while
respecting this repo's specialisation, isolation, composability, and lazy-loading
principles.

### Why a skill (and not a prompt or an agent)

This repo's conventions are explicit: there are no `agents/` or `commands/`
folders; any new capability belongs inside a skill, expressed as actions
(`conventions.md § Skill Invocation`). hve-core's prompt+agent+chatmode spread is
exactly the "legacy baggage" `[D3]` this model is designed to replace. A skill
gives us slash-command and keyword invocation, lazy-loaded rules, and a clean
registry entry — all already supported.

### Dual-mode is the central requirement, and it is cheap

The user's hard requirement is that research works standalone *and* inside a work
package. Both `[D1]` and `[D3]` already detect output context; we only need to
adapt the detection to this repo's two locations. Crucially, the **dossier
content is identical** in both modes — only the resolved path differs — so this
is a single template plus one location-resolution rule, not two parallel
pipelines.

### What I would explicitly not do in v1

- Not build jakkaj's full 8-subagent swarm (over-engineered for most research).
  v1 ships only `single` (default) and `focused-swarm` (a small set of
  independent lenses); the typed 8-agent `full-swarm` is a future enhancement —
  it is not advertised as a selectable mode until its worker roles, source
  capture, and synthesis rules are defined `[D1]`.
- Not build the workshop artifact or external-research-prompt generators yet
  (real value, but additive; record as future enhancements) `[D1]`.
- Not re-implement interrogation — compose `grill-me` `[D2]`.
- Not invent a new tracking folder — reuse `project/` artifact layout, never an
  hve-core-style `.copilot-tracking/` `[D3]`.

## 4. Proposed Design

### 4.1 Skill shape

```text
$AGENT_SYSTEM_FOLDER/skills/research/
├── SKILL.md
├── actions/
│   ├── create-research.md       # run research -> write one cited dossier
│   ├── synthesize-research.md   # >=2 dossiers -> summary + position doc
│   └── prepare-handoff.md       # emit a handoff recommendation + route to owning skill
├── rules/
│   ├── rules.index.md           # marks citations.md + core.md read-only boundary as Non-Overridable
│   ├── core.md                  # artifact-first, read-only boundary, modes, location resolution
│   ├── citations.md             # [F##] source index, evidence discipline, read-don't-guess
│   └── dossier.md               # dossier structure + naming + location conventions
└── templates/
    ├── research-dossier.md      # single topic dossier (both modes)
    └── summary-position.md      # cross-dossier synthesis (this very document's shape)
```

Suggested manifest frontmatter (mattpocock-style, trigger-first). This is the
frontmatter only — the full `SKILL.md` must follow
`$AGENT_SYSTEM_SKILL/rules/skill.md` (Variables, Purpose, Configuration with
Capabilities + Rules index pointer + Bundled Templates `Load when`, and Flow),
using variable-based action/template paths:

```yaml
---
name: research
description: Produce citation-backed research, investigation, and synthesis artifacts from code, docs, and web sources, standalone or inside a work package. Use when the user wants to research, investigate, create a dossier, compare references, gather evidence, or synthesise findings before planning work.
---
```

### 4.2 Capabilities

| Capability | Action | Description | Load when |
|---|---|---|---|
| Create Research | `create-research.md` | Resolve output location, run research (mode: `single` default, or `focused-swarm` for a few independent lenses), write one cited dossier from the template, stop at the read-only boundary | User wants to research/investigate a topic and capture a dossier |
| Synthesize Research | `synthesize-research.md` | Across two or more dossiers in a campaign, produce a summary + position document with an opinion and a recommendation/plan | User has multiple research dossiers and wants a consolidated position |
| Prepare Handoff | `prepare-handoff.md` | Emit a handoff recommendation (what to plan/track/record) and route to the owning skill; the research skill owns only the recommendation, not issue/DR/work-package creation logic | User wants to act on research (plan, track, or record a decision) |

`grill-me` is referenced (not duplicated) from `create-research` whenever scope
or requirements are ambiguous and the answer cannot be found in sources. The
research skill never mutates non-research artifacts: `prepare-handoff` produces a
recommendation and invokes `work-tracking` / `decision-records`, which own their
own outputs. This keeps the read-only research boundary intact.

### 4.3 Skill rules (the strict core)

Rules are skill rules (immutable per project). To stop a future project rule from
weakening the invariants the design depends on, `rules.index.md` MUST list
`citations.md` and the read-only-boundary portion of `core.md` under a
`## Non-Overridable Rules` section (per `conventions.md § Precedence Model`).
Location policy and cross-reference conventions remain overridable.

| Rule | Enforces | Overridable |
|---|---|---|
| `core.md` | Artifact-first output; read-only research boundary (no implementation edits during research); decomposition modes and when each applies; output-location resolution (standalone vs work-package); how focused-swarm worker findings are captured, cited, and synthesised into one dossier; completion criteria (enough evidence to plan) | Partly — read-only boundary is non-overridable; modes/location policy overridable |
| `citations.md` | Mandatory `[F##]` source index referenced inline; local workspace refs as plain paths (no `#file:` directives), external refs as markdown links; "read sources, do not guess"; evidence may be file paths with line ranges, command/test/benchmark output, or repro steps — each labelled; separate direct evidence from inference | No (non-overridable) |
| `dossier.md` | Required dossier sections (executive summary, key insights, body, **Alternatives Considered** with selected/rejected rationale, **Gaps / Next Research**, **Evidence vs Inference**, sources); naming `YYYY-MM-DD-<topic>.md`; standalone campaign folder vs work-package `research/` placement | Yes |

### 4.4 Output-location convention

Standalone research **always** uses a dated folder (even for a single topic) so a
later synthesis or second dossier has a home without restructuring. Work-package
research stays **flat** to match the existing work-package convention — no nested
campaign folder unless `work-tracking/rules/work-package.md` is changed.

| Mode | Path | When | Detection |
|---|---|---|---|
| Standalone | `project/research/YYYY-MM-DD-<slug>/` containing `YYYY-MM-DD-<topic>.md` dossiers and, when applicable, `YYYY-MM-DD-summary-and-position.md` | Research not tied to a work package | No active/linked work package in context |
| Work package | `project/work/<wp>/research/YYYY-MM-DD-<topic>.md` (flat); a synthesis lives alongside as `project/work/<wp>/research/YYYY-MM-DD-summary-and-position.md` | Research for an active issue/work package | Inside or linked to `project/work/<wp>` |

This is consistent with the work-tracking skill's existing
`research/YYYY-MM-DD-topic.md` convention (`work-tracking/rules/work-package.md
§ research/`), and the standalone dated-folder form is exactly the structure this
research effort itself used — a useful dogfooding proof.

### 4.5 Registry and variable wiring

| Change | File |
|---|---|
| Define `RESEARCH_SKILL := $AGENT_SYSTEM_FOLDER/skills/research` | `$AGENT_SYSTEM_FOLDER/AGENTS.md § Variables` |
| Add registry row: Research / keywords `research, investigate, dossier, evidence, sources, synthesise` / actions `create-research, synthesize-research, prepare-handoff` | `$AGENT_SYSTEM_FOLDER/AGENTS.md § Skill Catalog` |
| Optional: note research outputs in project docs map | root `AGENTS.md § Project System` |

### 4.6 Project rules tier (optional, light)

Mirror the work-tracking pattern only if the repo needs to customise research
output policy: a small `$AGENT_SYSTEM_FOLDER/rules/research/` domain (mutable)
for repo-specific output-location policy and cross-references into
`learnings.md`, `decision-records/`, and `future-enhancement-ideas.md`. The
invariants (citations, durability, read-only) stay in the skill rules, with
citations and the read-only boundary marked non-overridable.
Do not add this tier unless a real deviation appears.

## 5. Recommended Plan (phased)

| Phase | Outcome | Notes |
|---|---|---|
| 0. Decide | Accept this position; open a work package + issue via `work-tracking` | This document is the research input; `related:` set at plan time |
| 1. Author skill scaffold | `research/SKILL.md` + `rules.index.md` + `core.md` + `citations.md` + `dossier.md` + 2 templates, via the `agent-system` skill (author action) | Follow `agent-system` templates and `conventions.md` (no emojis, Variables blocks, no `---` rules) |
| 2. Author `create-research` | Location resolution + modes + read-only boundary + template fill | Default mode `single`; `focused-swarm` for independent lenses; `full-swarm` not exposed yet |
| 3. Author `synthesize-research` + `prepare-handoff` | Cross-dossier synthesis; handoff recommendation that routes to `work-tracking` / `decision-records` (which own their outputs) | `synthesize-research` mirrors this document's shape |
| 4. Wire registry | `AGENTS.md` variable + registry row | Keyword + slash-command invocation |
| 5. Validate | `agent-system` review action against bundled checklist; dry-run both modes | Add a learning entry if patterns emerge |
| 6. Future enhancements | Workshop artifact; external-research prompt generator; typed `full-swarm` roster | Record in `future-enhancement-ideas.md` |

### Acceptance criteria for the eventual skill

- A user can run research standalone and get a dated, cited dossier under
  `project/research/`.
- The same skill, invoked inside a work package, writes to
  `project/work/<wp>/research/` with identical dossier content.
- Every dossier carries a `[F##]` source index; claims are evidence-linked.
- Research stops at a read-only boundary and hands off explicitly.
- Multiple dossiers can be synthesised into a summary + position document.
- The skill passes the `agent-system` review checklist.

## 6. Risks and Mitigations

| Risk | Mitigation |
|---|---|
| Over-building (swarm, workshops, external prompts all at once) `[D1]` | Ship the 3-action core; defer the rest to future enhancements |
| Citation discipline degrades to prose confidence `[D2]` | Make `[F##]` index and read-don't-guess non-overridable rules, checked by the review action |
| Path/convention drift between standalone and work-package `[D3]` | Single template + one location-resolution rule; standalone always a dated folder, work-package always flat; identical content both modes |
| Duplicating existing capabilities (interrogation, tracking) | Compose `grill-me`, `work-tracking`, `decision-records`; `prepare-handoff` only recommends and routes — it never owns their outputs |
| Default swarm cost/noise `[D1]` | `single` is the default mode; `focused-swarm` is explicit opt-in; `full-swarm` deferred until its roles/synthesis are defined |

## Sources

- **[D1]** jakkaj/tools research dossiers and workshop research — sibling dossier:
  `2026-05-31-jakkaj-tools-research-dossiers.md` (this folder). Upstream:
  <https://github.com/jakkaj/tools>
- **[D2]** mattpocock/skills research-capability patterns — sibling dossier:
  `2026-05-31-mattpocock-skills-research.md` (this folder). Upstream:
  <https://github.com/mattpocock/skills>
- **[D3]** microsoft/hve-core RPI Research capability — sibling dossier:
  `2026-05-31-hve-core-rpi-research.md` (this folder). Upstream:
  <https://github.com/microsoft/hve-core>
- **Repo conventions consulted:**
  `$AGENT_SYSTEM_FOLDER/AGENTS.md`;
  `$AGENT_SYSTEM_FOLDER/skills/agent-system/rules/conventions.md`;
  `$AGENT_SYSTEM_FOLDER/skills/agent-system/templates/{skill,action,rule}.md`;
  `$AGENT_SYSTEM_FOLDER/skills/work-tracking/rules/work-package.md`;
  `$AGENT_SYSTEM_FOLDER/skills/grill-me/SKILL.md`.
- **Interim format reference:**
  `ai-agent-system/handoff/project/work/2026-05-30-alpine-devcontainer/research/2026-05-30-alpine-devcontainer.md`
