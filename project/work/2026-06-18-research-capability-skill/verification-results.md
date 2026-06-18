# Verification Results — Research Capability Skill

Related: issue #6

This document records Phase 5 validation for the `research` agent-system skill: the
`agent-system` review outcome, dry-run walkthroughs of every location and research mode,
and a mapping of each issue #6 acceptance criterion to a Pass/Fail result with evidence.

## 1. Agent-System Review (REQ019)

The `agent-system` `review` action was run against the bundled `validation.md` checklist over
every authored asset in `.agents/skills/research/` plus the two modified `AGENTS.md` registries.

| Asset | Type | Result |
|-------|------|--------|
| `.agents/skills/research/SKILL.md` | Skill manifest | Pass |
| `.agents/skills/research/actions/create-research.md` | Action | Pass |
| `.agents/skills/research/actions/synthesise-research.md` | Action | Pass |
| `.agents/skills/research/rules/core.md` | Topic rule | Pass |
| `.agents/skills/research/rules/citations.md` | Topic rule | Pass |
| `.agents/skills/research/rules/dossier.md` | Topic rule | Pass |
| `.agents/skills/research/rules/rules.index.md` | Rules index | Pass |
| `.agents/skills/research/templates/research-dossier.md` | Output-doc template | Pass (cross-cutting) |
| `.agents/skills/research/templates/summary-position.md` | Output-doc template | Pass (cross-cutting) |
| `.agents/AGENTS.md` | Agent-system AGENTS.md | Pass |
| `AGENTS.md` (root) | Root AGENTS.md | Pass |

Checks performed and their results:

- **Universal conventions** — Every asset has a `## Variables` block (output-doc templates are
  excluded, matching the `decision-record.md` reference); all `$VARIABLE` references
  (`$AGENT_SYSTEM_FOLDER`, `$PROJECT_FOLDER`, `$RESEARCH_SKILL`, `$GRILL_ME_SKILL`) are declared
  in `.agents/AGENTS.md`; no headings deeper than H3; no emojis or arrow glyphs; no `---`
  separators outside SKILL.md frontmatter.
- **Skill manifest** — Capabilities table carries `Load when` triggers; Rules section is an
  index pointer (no eager `### Bundled Rules` load); Bundled Templates listed with `Load when`;
  Flow routes intent to a capability, then loads capability-matched rules, then templates.
- **Actions** — Both carry `## Variables`, `## Purpose`, `## Flow` with a Prerequisites note,
  numbered `### Step` headings each with a `**Goal:**`, and an `## Error Handling` table.
- **Topic rules** — Each leads with a Quick Reference table and stays within its topic.
- **Rules index** — Lists every topic rule; the links resolve; a `## Non-Overridable Rules`
  section names `citations.md` and the read-only boundary.
- **Cross-cutting** — Every required section in `dossier.md` maps to a heading in
  `research-dossier.md` (Metadata is the title-block table, per the decision-record convention),
  and every template heading traces back to a rule. `summary-position.md` sections are
  enumerated in `synthesise-research.md` Step 4.
- **Registry consistency** — All seven skill variables in `.agents/AGENTS.md` have a Skill
  Catalog row and every catalog row references a declared variable.

**No Critical or Compliance findings. No unresolved findings.**

## 2. Dry-Run Walkthroughs (REQ020)

These are tabletop traces through `create-research.md` (and `synthesise-research.md`); they
exercise the decision points without writing files (research is read-only and writes only on
user approval).

### Location modes

| Mode | Trigger | Resolved path (Step 1) |
|------|---------|------------------------|
| Standalone | User asks for standalone research, or no work-package context | `project/research/YYYY-MM-DD-<slug>/` |
| Work-package | User names a work package, or runs inside/linked to one | `project/work/<wp>/research/` (flat) |

In both modes Steps 2–6 are identical; only the Step 1 path differs, so dossier **content** is
the same across locations (core.md § Output-Location Resolution).

### Research modes

| Combination | Trace | Outcome |
|-------------|-------|---------|
| Standalone + `single` | Step 1 standalone path; Step 2 no lenses described, default `single`; Steps 3–5 gather cited evidence and fill `research-dossier.md`; Step 6 stop at boundary | One dated `[F##]`-cited dossier at `project/research/YYYY-MM-DD-<slug>/YYYY-MM-DD-<topic>.md` |
| Work-package + `single` | Step 1 work-package path; rest as above | Same dossier content written flat under `project/work/<wp>/research/` |
| Standalone + `focused-swarm` | Step 2 user names independent lenses; Step 3 each lens researched and cited independently; Step 4 synthesised into **one** dossier with a deduplicated combined `[F##]` index | One dossier per core.md § Focused-Swarm Capture and Synthesis |
| Work-package + `focused-swarm` | As above with the work-package path | One flat dossier with combined index |
| `full-swarm` (any location) | Step 2 / Error Handling | Declined as not selectable; `focused-swarm` offered instead |

### Synthesis walkthrough

`synthesise-research.md` consumes one or more existing dossiers, references `grill-me` before
forming a position, and writes a `summary-position` document containing position, opinion, and a
"Gaps / Next Research" section, with no action or routing recommendations. Works on a single
dossier as well as several.

## 3. Acceptance Criteria

| AC | Result | Evidence |
|----|--------|----------|
| **AC001** — Standalone produces a dated `[F##]`-cited dossier under `project/research/<dated-slug>/` from the template | Pass | `create-research.md` Step 1 (standalone path), Step 5 (fill `research-dossier.md`, `YYYY-MM-DD-<topic>.md`, mandatory `[F##]` index); `dossier.md` placement; Dry-run "Standalone + single" |
| **AC002** — Same skill for a work package writes identical content to `project/work/<wp>/research/` (flat) | Pass | `create-research.md` Step 1 (work-package path); `core.md § Output-Location Resolution` (only path differs); Dry-run "Work-package + single" |
| **AC003** — `single` (default) + `focused-swarm`; `full-swarm` not selectable | Pass | `create-research.md` Step 2 table + Error Handling; `core.md § Research Modes`; Dry-run modes table |
| **AC004** — `synthesise-research` consumes one or more dossiers, emits `summary-position` with position, opinion, Gaps/Next Research, no routing | Pass | `synthesise-research.md` steps; `summary-position.md` template; Synthesis walkthrough |
| **AC005** — Mandatory `[F##]` index; local plain paths with line ranges, external markdown links; read-only boundary | Pass | `citations.md` (reference formats, read-don't-guess); `dossier.md` required Sources; `core.md § Read-Only Research Boundary`; `create-research.md` Steps 3 and 6 |
| **AC006** — `core.md`, `citations.md`, `dossier.md` exist; `rules.index.md` lists `citations.md` + read-only boundary under `## Non-Overridable Rules` | Pass | Files present; `rules.index.md` § Non-Overridable Rules |
| **AC007** — Both actions reference (not duplicate) `grill-me` | Pass | `create-research.md` Step 2 + Error Handling; `synthesise-research.md`; both reference `$GRILL_ME_SKILL` |
| **AC008** — `RESEARCH_SKILL` defined + registry row added; skill passes the `agent-system` review checklist | Pass | `.agents/AGENTS.md` variable (line 11) + Research catalog row (line 63); Section 1 review all-pass |

**All eight acceptance criteria pass.**
