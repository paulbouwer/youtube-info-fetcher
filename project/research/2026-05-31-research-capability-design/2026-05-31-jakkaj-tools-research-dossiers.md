# Research Dossier: jakkaj/tools Research Dossiers and Workshop Research Capabilities

| Field | Value |
|---|---|
| Generated date | 2026-05-31 |
| Research Query | Analyse `https://github.com/jakkaj/tools`, specifically its research dossier and workshop research capabilities, including agents, prompts, skills, rules, templates, orchestration, storage, naming, and citations. |
| Mode | standalone research |
| Scope | `jakkaj/tools` at commit `8bac9bf51d3337e2093a413e2065f94dc1efe574`, with focus on `skills/SDD/*`, deprecated `agents/commands/*`, `README.md`, `README_AGENTS.md`, and sample generated artifacts under `docs/plans/*`. |
| Method | GitHub repo tree listing, targeted GitHub code search, and direct reads of command/skill bodies and representative generated artifacts. Findings cite exact repository paths. |

## 1. Executive Summary

This dossier answers how `jakkaj/tools` initiates, decomposes, stores, and uses research artifacts. The repository has both legacy slash-command prompt files under `agents/commands/` and current installable `SKILL.md` packages under `skills/SDD/`; the current research workflow is best represented by `skills/SDD/plan-1a-v2-explore/SKILL.md`, `skills/SDD/flowspace-research-v2/SKILL.md`, `skills/SDD/plan-2c-v2-workshop/SKILL.md`, and `skills/SDD/the-flow/SKILL.md` [F04] [F05] [F06] [F08].

### Headline findings

1. **Research dossier is a pre-specification grounding artifact.** `/plan-1a` researches existing code before `/plan-1b`, saves `docs/plans/<ordinal>-<slug>/research-dossier.md`, and explicitly stops without implementing [F04].
2. **The main dossier workflow is swarm-style.** Current `plan-1a-v2` launches 8 specialized parallel subagents: implementation, dependencies, patterns, quality/testing, interfaces, documentation/history, prior learnings, and domain/boundary scouting [F04].
3. **`flowspace-research-v2` is a worker, not an orchestrator.** It is designed to be called by a parent command/subagent; it does not launch subagents, and emits structured findings for synthesis [F05].
4. **Workshop research differs from dossier research.** A dossier answers “what exists and what risks matter?”; a workshop answers “what design should we keep open while building?” and becomes an authoritative design reference for `/plan-3` [F06] [F07] [F08].
5. **The modern package format is skill-like but monolithic.** Skills live as recursive `skills/<category>/<slug>/SKILL.md` packages distributed by `npx skills`, but their steps, rules, and templates are embedded in one file rather than split into `actions/`, `rules/`, and `templates/` [F02] [F03].

### Quick stats

| Item | Finding |
|---|---|
| Primary current research skill | `skills/SDD/plan-1a-v2-explore/SKILL.md` |
| Supporting research worker | `skills/SDD/flowspace-research-v2/SKILL.md` |
| Workshop skill | `skills/SDD/plan-2c-v2-workshop/SKILL.md` |
| Legacy equivalents | `agents/commands/plan-1a-explore.md`, `agents/commands/flowspace-research.md`, `agents/commands/plan-2c-workshop.md` |
| Research decomposition | 8 parallel subagents in current skill; legacy command used 7 |
| Dossier location | `docs/plans/<ordinal>-<slug>/research-dossier.md` |
| External research location | `docs/plans/<ordinal>-<slug>/external-research/<topic-slug>.md` |
| Workshop location | `docs/plans/<ordinal>-<slug>/workshops/<NNN>-<topic-slug>.md` in v2 skill |
| Citation style in generated artifacts | Mixed: file paths, file:line references, numbered external citations in external research; not a universal `[F##]` source index |

## 2. Repository artifact map

`jakkaj/tools` is now primarily a **skills repository**. Its root README says skills are the product, distributed through `npx skills`, while `setup.sh` only installs developer tooling such as FlowSpace and MCP servers [F01]. The high-level structure identifies current skills under `skills/SDD/`, `skills/harness/`, `skills/general/`, and `skills/personal/`; it marks `agents/commands/` and `agents/commands-lite/` as deprecated legacy command directories [F01]. The public catalog repeats that skills use `skills/<category>/<slug>/SKILL.md`, are recursively discovered, and are flattened by slug at install time [F03].

| Artifact class | Current path(s) | Role in research workflow |
|---|---|---|
| Front-door flow | `skills/SDD/the-flow/SKILL.md` | Routes the user into `/plan-1a`, `/plan-1b`, optional workshops, architecture, implementation, review, and merge [F08]. |
| Research dossier skill | `skills/SDD/plan-1a-v2-explore/SKILL.md` | Main codebase research orchestration and dossier generation [F04]. |
| Research worker skill | `skills/SDD/flowspace-research-v2/SKILL.md` | Focused FlowSpace-first worker for parent synthesis [F05]. |
| Workshop skill | `skills/SDD/plan-2c-v2-workshop/SKILL.md` | Generates detailed design/workshop documents [F06]. |
| Spec consumer | `skills/SDD/plan-1b-v3-specify-and-clarify/SKILL.md` | Reads `research-dossier.md` and external research into spec context [F07]. |
| Architecture consumer | `skills/SDD/plan-3-v3-architect/SKILL.md` | Reads research and workshops; treats workshops as authoritative [F09]. |
| Legacy prompts | `agents/commands/*.md` | Deprecated but still useful for historical template comparison [F10] [F11] [F12]. |
| Sample outputs | `docs/plans/*/research-dossier.md`, `docs/plans/*/workshops/*.md`, `docs/plans/*/external-research/*.md` | Show real artifact conventions [F13] [F14] [F15]. |

## 3. How research is initiated and orchestrated

### 3.1 Entry points and routing

The current user-facing path is usually through `the-flow`, whose description says it drives the SDD pipeline `/plan-1a → 1b → [2c] → [2d] → 3 → 5 → 6 → 7 → 8` and chooses `/plan-1a` for research-worthy intents or `/plan-1b` for clear asks [F08]. The flow table treats `research-dossier.md` as the artifact that moves the state from `awaiting-1a` to `awaiting-1b` [F08].

Direct invocation is also supported. `plan-1a-v2-explore` accepts:

```text
/plan-1a-explore "research query"
/plan-1a-explore --plan <name> "research query"
/plan-1a-explore --console "research query"
```

Its behavior is: default auto-detect/create a plan folder, `--plan` use/create a specified plan folder, and `--console` emit report without files [F04].

### 3.2 Output context detection

`plan-1a-v2` determines whether to write a file by checking flags, then plan context: ordinal branch names, current working directory under `docs/plans/<NNN>-*`, recent conversation plan references, and finally a slug generated from the query [F04]. The default plan-mode output is explicitly:

```text
docs/plans/<ordinal>-<slug>/research-dossier.md
```

Good: infer a stable destination from branch/folder context so a research artifact is automatically co-located with later spec/plan artifacts.  
Bad: port this exact `docs/plans/` location unchanged if the target system uses `project/research/` and `project/work/<wp>/research/`.

### 3.3 Swarm decomposition

The current `plan-1a-v2` instructs the parent to use parallel subagent execution and launch **8 specialized subagents in a single message** [F04]. The roles are:

| ID prefix | Subagent | Main job | Output convention |
|---|---|---|---|
| `IA` | Implementation Archaeologist | Entry points, algorithms, data flow, initialization | `IA-01` through `IA-10` |
| `DC` | Dependency Cartographer | Imports, consumers, external services, storage/config dependencies | `DC-01` through `DC-10` |
| `PS` | Pattern & Convention Scout | Design patterns, error handling, logging, naming, architecture | `PS-01` through `PS-10` |
| `QT` | Quality & Testing Investigator | Unit/integration/e2e tests, performance, debt | `QT-01` through `QT-10` |
| `IC` | Interface & Contract Analyst | Public APIs, schemas, events, compatibility | `IC-01` through `IC-10` |
| `DE` | Documentation & Evolution Historian | READMEs, docs, ADRs, comments, migration history | `DE-01` through `DE-10` |
| `PL` | Prior Learnings Scout | Mines prior discoveries, gotchas, retros, execution logs | `PL-01` through `PL-15` |
| `DB` | Domain & Boundary Scout | Existing/potential domains, domain boundaries, contracts | `DB-01` through `DB-08` |

The synthesis step expects about 60-85 findings, deduplicates, prioritizes by impact, validates code references, highlights prior learnings, and identifies external research opportunities [F04].

### 3.4 FlowSpace worker pattern

`flowspace-research-v2` is explicitly a **parallel subagent worker**: it “IS the subagent” and “does NOT launch subagents” [F05]. It supports query arguments such as `--scope`, `--exclude`, `--limit`, `--mode`, and `--graph`; it auto-detects path/symbol/concept queries, uses FlowSpace `tree/search/get_node` when available, and falls back to Glob/Grep/Read [F05]. Its output is structured for parent synthesis with metadata, key nodes, code excerpts, relationships, and gaps/questions [F05].

Good: separate “orchestrator skill” from “research worker action” so parent commands can compose focused research.  
Bad: make every research invocation launch its own swarm; use a worker when the scope is already narrow.

## 4. The research dossier artifact and template structure

### 4.1 Dossier purpose

The root README describes `/plan-1a-explore` as “research before specification,” producing a research dossier or console output and using parallelism to cover the landscape [F01]. The conceptual purpose is to turn vague intent into high-fidelity context: how similar code works, which dependencies matter, what tests/interfaces exist, what conventions apply, and what documentation says or fails to say [F01].

The current skill’s generated report structure starts:

```markdown
# Research Report: [RESEARCH_TOPIC]

**Generated**: [ISO-8601 timestamp]
**Research Query**: "[Original user input]"
**Mode**: [Research-Only | Pre-Plan | Plan-Associated]
**Location**: [Output path]
**FlowSpace**: [Available/Not Available]
**Findings**: [Total count]
```

It then includes the major sections below [F04].

### 4.2 Dossier section headings

Verbatim/high-value structure from `plan-1a-v2`:

| Section | Purpose |
|---|---|
| `## Executive Summary` | What it does, business purpose, key insights, quick stats |
| `## How It Currently Works` | Entry points, core execution flow, data flow, state management |
| `## Architecture & Design` | Component map, core components, design patterns, boundaries |
| `## Dependencies & Integration` | Internal/external dependencies, consumers, integration architecture |
| `## Quality & Testing` | Coverage, test strategy, issues/debt, performance |
| `## Modification Considerations` | Safe areas, caution zones, danger zones, extension points |
| `## Prior Learnings (From Previous Implementations)` | Institutional gotchas and resolutions from earlier work |
| `## Domain Context` | Existing domains, domain map position, potential domain actions |
| `## Critical Discoveries` | Critical/high impact findings with source subagents |
| `## Supporting Documentation` | Related docs, key comments, historical context |
| `## Recommendations` | If modifying/extending/refactoring |
| `## External Research Opportunities` | Gaps requiring non-code research plus ready-to-use `/deepresearch` prompts |
| `## Appendix: File Inventory` | Core files, tests, config |
| `## Next Steps` | Path forward based on mode and research gaps |

The legacy `agents/commands/plan-1a-explore.md` has substantially the same shape but only 7 subagents and no v2 domain/harness additions [F10].

### 4.3 Stop rule and read-only boundary

`plan-1a-v2` explicitly says it is read-only: after outputting the research report it must not proceed to `/plan-1b`, make code changes, create additional files, or implement recommendations [F04]. This is a strong rule worth porting as a skill rule.

Good: “research produces evidence, not code.”  
Bad: have a research skill silently transition into implementation, because it collapses exploration, decision-making, and action into one opaque loop.

## 5. Workshop research artifact

### 5.1 Workshop purpose and invocation

`plan-2c-v2-workshop` creates detailed design documents for complex concepts from spec Workshop Opportunities or ad hoc topics needing deep exploration [F06]. Expected modes are:

```text
/plan-2c-workshop <plan> "<topic>"
/plan-2c-workshop <plan> --from-spec
/plan-2c-workshop <plan> --list
```

It says workshops are working reference documents that a developer, reviewer, stakeholder, or agent would keep open during implementation [F06]. It should be used when concepts have multiple valid approaches, interfaces/contracts need specification, data structures are cross-component, CLI/UX has branching state, storage formats matter, schema migrations matter, or stakeholder alignment is needed [F06].

### 5.2 Workshop differs from dossier

| Dimension | Research dossier | Workshop research |
|---|---|---|
| Primary question | “What exists, what matters, what risks constrain the work?” | “What design/reference should guide implementation?” |
| Timing | Before specification or early planning | After spec identifies workshop opportunities; before architecture, or ad hoc anytime |
| Decomposition | Parallel subagents, broad codebase research | Single topic deep dive, flexible sections by workshop type |
| Output | `research-dossier.md` | `workshops/<NNN>-<topic-slug>.md` |
| Downstream role | Informs spec, complexity, architecture, external research | Authoritative design decision source for architecture |
| Evidence style | Findings with code refs, dependencies, risks, prior learnings | Examples, diagrams, schemas, decision space, proof/evidence ledger |

The current `plan-3-v3-architect` explicitly reads `workshops/*.md` as **authoritative design decisions**, must not contradict them, and skips research for workshopped topics [F09].

### 5.3 Workshop template structure

Current `plan-2c-v2` requires this header shape [F06]:

```markdown
# Workshop: [Topic Name]

**Type**: [CLI Flow | Data Model | API Contract | State Machine | Integration Pattern | Storage Design | Other]
**Plan**: [ordinal-slug]
**Spec**: [link to spec]
**Created**: [ISO-8601]
**Status**: Draft | Review | Approved

**Value Thesis**: [How this workshop makes validated work cheaper, safer, clearer, or more repeatable]
**Target Proof Level**: Orientation | Decision Space | Preferred Direction | Contract Ready | Implementation Ready | Validated
**Current Proof Level**: Orientation | Decision Space | Preferred Direction | Contract Ready | Implementation Ready | Validated

**Selected Value Axes**:
- **[Axis 1]**: [Why this axis matters for this workshop]
- **[Axis 2]**: [Why this axis matters for this workshop]
- **[Axis 3]**: [Why this axis matters for this workshop]

**Related Documents**:
- [Link to related workshop or external doc]

**Domain Context** (if domains exist):
- **Primary Domain**: [domain this workshop's topic belongs to]
- **Related Domains**: [domains that consume or are consumed by this topic]
```

Recommended shared sections include `## Value Frame`, `## Evidence Ledger`, `## Decision Space`, `## Attention Reduction`, and `## Validation / Acceptance` [F06]. Type-specific patterns include CLI flow, data model, and state machine structures with command tables, ASCII diagrams, mermaid diagrams, schema definitions, validation rules, and transition tables [F06] [F12].

### 5.4 Workshop proof/value framing

The v2 workshop skill adds a strong value lens absent from the legacy command: it asks the workshop to identify 3-5 value axes and a target/current proof level. Proof levels progress from `Orientation` to `Decision Space`, `Preferred Direction`, `Contract Ready`, `Implementation Ready`, and `Validated` [F06]. This makes the artifact measurable by downstream usefulness rather than document volume.

Good: adopt “Target Proof Level” and “Evidence Ledger” for research that must guide implementation.  
Bad: require every research note to become a workshop; broad dossiers and focused workshops serve different purposes.

## 6. Storage, naming, and citation conventions

### 6.1 Storage and naming

| Artifact | Current convention in `jakkaj/tools` | Evidence |
|---|---|---|
| Plan folder | `docs/plans/<NNN>-<slug>/` | `plan-1a-v2` context detection and output mode [F04] |
| Dossier | `docs/plans/<NNN>-<slug>/research-dossier.md` | `plan-1a-v2`, `the-flow`, examples [F04] [F08] [F13] |
| External research | `docs/plans/<NNN>-<slug>/external-research/<topic-slug>.md` | `plan-1a-v2` external research opportunities [F04], sample [F15] |
| Workshop | `docs/plans/<NNN>-<slug>/workshops/<NNN>-<topic-slug>.md` | v2 workshop skill [F06] and samples [F14] |
| Legacy workshop | `docs/plans/<NNN>-<slug>/workshops/<topic-slug>.md` | legacy command [F12] |
| Skill source | `skills/<category>/<slug>/SKILL.md` | README and catalog [F01] [F03] |
| Legacy command source | `agents/commands/*.md` | README marks deprecated [F01] |

The target system’s desired `project/research/<dated-slug>/YYYY-MM-DD-topic.md` and `project/work/<wp>/research/YYYY-MM-DD-topic.md` convention is not present in `jakkaj/tools`; their storage is plan-centric, not dual-location.

### 6.2 Citation/source handling

Citation handling is useful but inconsistent:

| Artifact type | Source/citation behavior |
|---|---|
| Research dossier template | Requires findings to have code references and source subagent IDs, but no universal source index [F04]. |
| FlowSpace worker output | Uses graph, node ID, location, code excerpts, relationships, and gaps [F05]. |
| Workshop samples | Cite related documents and paths in the header; evidence appears as file:line references or links where needed [F14]. |
| External research sample | Has a `## Citations` section, direct source-code reads, and numbered secondary citations from Perplexity [F15]. |

Good: preserve file paths, node IDs, and source IDs close to claims.  
Good: add a final `## Sources` index for portability, because `jakkaj/tools` does not enforce one consistently.  
Bad: rely on subagent finding IDs alone as citations; they identify provenance inside the run, not independently verifiable sources.

## 7. Supporting artifacts: agents, prompts, skills, rules, templates

### 7.1 Agents and personas

There are no separate “agent persona definition” files for the research swarm. The personas are embedded as prompt text inside `plan-1a-v2` and legacy `plan-1a-explore`: Implementation Archaeologist, Dependency Cartographer, Pattern & Convention Scout, Quality & Testing Investigator, Interface & Contract Analyst, Documentation & Evolution Historian, Prior Learnings Scout, and Domain & Boundary Scout [F04] [F10].

`flowspace-research-v2` is itself framed as a worker/subagent persona and explicitly not an orchestrator [F05].

### 7.2 Prompts and slash commands

Legacy prompts live under `agents/commands/` and `agents/commands-lite/`, but README marks both as deprecated and points to `skills/` as the replacement [F01]. The command bodies are still valuable because they show earlier, less domain-aware forms of the same workflow [F10] [F11] [F12].

The current slash-command-like invocation is generated by skills installed from `SKILL.md` packages. README_AGENTS shows examples installing specific skills such as `plan-1a-v2-explore`, `plan-1b-v3-specify-and-clarify`, and `plan-3-v3-architect` [F03].

### 7.3 Skills

The current repository is organized around `SKILL.md` packages. Relevant skills include:

| Skill | Role |
|---|---|
| `plan-1a-v2-explore` | Research dossier orchestration |
| `flowspace-research-v2` | Focused codebase research worker |
| `deepresearch-v2` | Prompt-crafting helper for external deep research |
| `plan-1b-v3-specify-and-clarify` | Spec generation that incorporates research |
| `plan-2c-v2-workshop` | Workshop design docs |
| `plan-3-v3-architect` | Architecture/plan generation that consumes research/workshops |
| `the-flow` | Guided front-door over the pipeline |

This differs from the target model: `jakkaj/tools` skills are mostly single large `SKILL.md` files with embedded pseudo-code, rules, templates, and examples. The target model should split those into `actions/*.md`, `rules/*.md`, `templates/*.md`, and a lazy-loading `rules.index.md`.

### 7.4 Rules and conventions

Research-relevant rules are embedded inline:

| Rule/convention | Location |
|---|---|
| Plan-1a is read-only and must stop after output | `plan-1a-v2` [F04] |
| Parallel subagents should be launched in one message | `plan-1a-v2` [F04] |
| Every finding should have code references | `plan-1a-v2` success criteria [F04] |
| FlowSpace node IDs are opaque and must not be reconstructed | `flowspace-research-v2` exploration workflow [F05] |
| Workshops become authoritative design decisions | `plan-3-v3-architect` [F09] |
| Workshops use ordinals in v2 | `plan-2c-v2` [F06] |

## 8. Portability to a skill/action/rule/template model

### 8.1 Concrete mapping

| Target model element | Portable extraction from `jakkaj/tools` | Suggested target files |
|---|---|---|
| Skill manifest | `plan-1a-v2-explore` frontmatter + purpose | `skills/research/SKILL.md` |
| Dossier action | Plan-1a context detection, subagent launch, synthesis, write/stop flow | `actions/create-dossier.md` |
| Worker action | `flowspace-research-v2` path/symbol/concept workflow and fallback | `actions/research-worker.md` |
| Workshop action | Plan-2c modes, context gathering, proof/value frame, output | `actions/create-workshop.md` |
| External research action | `deepresearch-v2` prompt sections + plan-1a external opportunities | `actions/prepare-external-research.md` |
| Immutable rules | Read-only stop rule, citation requirements, no guessing, subagent provenance, workshop authority | `rules/research-output.md`, `rules/citations.md`, `rules/workshops.md` |
| Templates | Research report structure, worker finding format, workshop header/value frame | `templates/research-dossier.md`, `templates/research-worker-findings.md`, `templates/workshop.md` |
| Lazy loading | Short index mapping triggers to rules | `rules.index.md` |

### 8.2 Adopt

1. **Adopt the distinction between dossier and workshop.** Dossiers are broad context/risk research; workshops are focused design references with proof/evidence goals [F04] [F06].
2. **Adopt typed research perspectives.** IA/DC/PS/QT/IC/DE/PL/DB is a strong default swarm for broad codebase research [F04].
3. **Adopt a worker/orchestrator split.** Make a worker action for scoped symbol/path/concept research, and let the dossier action orchestrate [F05].
4. **Adopt external research opportunities.** Dossier output should identify what code cannot answer and produce ready-to-run external research prompts, with results stored separately [F04].
5. **Adopt workshop authority.** Once a workshop is accepted, the architecture/planning skill should not contradict it without an explicit superseding decision [F09].
6. **Adopt proof/value framing for workshops.** Target proof level, evidence ledger, and attention-reduction framing make workshop docs practical [F06].
7. **Adopt read-only boundaries.** Research should stop after writing the dossier and wait for explicit next action [F04].

### 8.3 Adapt

| `jakkaj/tools` pattern | Adaptation for target system |
|---|---|
| `docs/plans/<slug>/research-dossier.md` | Support both `project/research/<dated-slug>/YYYY-MM-DD-topic.md` and `project/work/<wp>/research/YYYY-MM-DD-topic.md`. |
| Plan ordinal folder detection | Replace with target work-package detection: if inside/linked to `project/work/<wp>`, write work-package research; otherwise standalone. |
| Single giant `SKILL.md` | Split into manifest, actions, rules, templates, and lazy-loading index. |
| Subagent count fixed at 8 | Make modes: `single`, `focused-swarm` (3-4), `full-swarm` (8), chosen by scope/complexity. |
| FlowSpace-specific guidance | Generalize to “code intelligence first, then rg/glob/view fallback.” |
| Mixed citation styles | Enforce `[F##]` source index for every dossier/workshop. |
| Workshop ordinals only | Use target date filenames for research; optionally keep ordinals inside work-package workshops if useful. |

### 8.4 Avoid

1. **Avoid binding research to a plan-only hierarchy.** The target needs standalone research and work-package research; plan-centric storage would be too narrow.
2. **Avoid hiding rules inside long prompt bodies.** Convert invariant behaviors into `rules/*.md` so they can be loaded and audited independently.
3. **Avoid treating FlowSpace as required.** Keep code-intelligence optional and define exact fallback behavior.
4. **Avoid inconsistent citations.** `jakkaj/tools` is strong on file references but not consistent on final source indexes; the target should be stricter.
5. **Avoid making workshops too ceremonial.** The v2 value/proof sections are useful, but only include sections needed to reach the target proof level.

### 8.5 Proposed target output conventions

Good:

```text
project/research/2026-05-31-agent-research-capability/
└── 2026-05-31-jakkaj-tools-research-dossiers.md
```

Good:

```text
project/work/012-research-capability/research/
└── 2026-05-31-jakkaj-tools-research-dossiers.md
```

Bad:

```text
docs/plans/012-research-capability/research-dossier.md
```

The bad example copies `jakkaj/tools` storage without respecting the target repo’s dual-location convention.

## 9. Gaps and verification limits

- I did not verify every historical generated dossier under `docs/plans/*/research-dossier.md`; I read representative examples and the authoritative generator skills.
- I did not find separate agent definition/persona files for research; personas appear embedded in skill/prompt bodies.
- I did not find a universal citation template requiring `[F##]` style source IDs; that structure is added in this dossier for portability.
- The repository has deprecated legacy commands and current skills. Where they differ, current `skills/SDD/*/SKILL.md` files should be treated as authoritative.

## Sources

| ID | Source |
|---|---|
| [F01] | `https://github.com/jakkaj/tools` repo root listing and `README.md` at commit `8bac9bf51d3337e2093a413e2065f94dc1efe574` |
| [F02] | `skills/SDD/` directory listing in `jakkaj/tools` |
| [F03] | `README_AGENTS.md` — public skill catalog, install matrix, skill layout, catalog entries |
| [F04] | `skills/SDD/plan-1a-v2-explore/SKILL.md` — current research dossier orchestration skill |
| [F05] | `skills/SDD/flowspace-research-v2/SKILL.md` — current FlowSpace-first research worker skill |
| [F06] | `skills/SDD/plan-2c-v2-workshop/SKILL.md` — current workshop skill |
| [F07] | `agents/commands/plan-1b-specify.md` and search hits for `skills/SDD/plan-1b-v3-specify-and-clarify/SKILL.md` — spec incorporation of research |
| [F08] | `skills/SDD/the-flow/SKILL.md` — front-door routing and artifact state table |
| [F09] | `skills/SDD/plan-3-v3-architect/SKILL.md` — research/workshop consumption in architecture |
| [F10] | `agents/commands/plan-1a-explore.md` — legacy research dossier command |
| [F11] | `agents/commands/flowspace-research.md` — legacy FlowSpace research worker command |
| [F12] | `agents/commands/plan-2c-workshop.md` — legacy workshop command |
| [F13] | `docs/plans/014-lite-agent-flows/research-dossier.md` — sample generated research dossier |
| [F14] | `docs/plans/014-lite-agent-flows/workshops/plan-3-research-subagent-rewrite.md` — sample generated workshop |
| [F15] | `docs/plans/022-skills-layout-migration/external-research/cli-paths-verification.md` — sample external research artifact with citations |
