# hve-core RPI Research Capability Dossier

| Field | Value |
|---|---|
| Generated date | 2026-05-31 |
| Research Query | Reference #3: how microsoft/hve-core implements the Research phase of Research/Plan/Implement capability across older, non-skill-style artifacts |
| Mode | standalone research |
| Scope | `microsoft/hve-core` RPI Research mechanics, agents, prompts, instructions, output artifacts, storage, citations, Research to Plan handoff, portability to a skill/action/rule/template model |
| Method | GitHub repository directory listing, GitHub code search, targeted reads of agents, prompts, instructions, collection manifests, README, and RPI documentation at commit `d2868d5a528025edc91b39f912e210ed391fd6c1` |

## 1. Executive Summary

This dossier answers how hve-core's older, non-skill-style RPI Research capability works and what can be ported into a self-contained skill model. The core finding is that Research is not a single prompt. It is a coordinated set of a slash-command prompt, a Task Researcher agent, a Researcher Subagent, RPI orchestration rules, tracking-file conventions, and user documentation.

### Headline findings

1. Research is triggered either by `/task-research` or by selecting the Task Researcher agent manually. The prompt frontmatter binds `/task-research` to `agent: Task Researcher`, and its requirements require scoping, alternatives evaluation, and a consolidated document for planning handoff [F01:1-19].
2. The Task Researcher is intentionally research-only. It may create and edit files only under `.copilot-tracking/research/`, delegates research tool usage to Researcher Subagent, and synthesizes subagent outputs into a primary research document [F02:19-35].
3. Research decomposes explicitly. Task Researcher instructs agents to run Researcher Subagent through `runSubagent` or `task`, parallelizing independent topics and treating subagent chat output as an index while full fidelity lives in subagent files [F02:33-48], [F03:62-74].
4. The output convention is artifact-first: a primary research document under `.copilot-tracking/research/{{YYYY-MM-DD}}/` plus optional subagent outputs under `.copilot-tracking/research/subagents/{{YYYY-MM-DD}}/` [F02:80-87].
5. The Plan phase consumes research by locating existing research, filling gaps with the same Researcher Subagent if needed, and linking plan objectives, details, discrepancies, and success criteria back to research findings [F05:158-180], [F05:193-201], [F05:537-549].
6. For portability, hve-core's content maps cleanly to a skill manifest, an `actions/research.md` action, immutable research rules, and a research document template. Its legacy baggage is mostly packaging and UI coupling: `.prompt.md` frontmatter, `.agent.md` handoff buttons, `.copilot-tracking/` locality, and emoji-rich response formats.

### Quick stats

| Item | Finding | Evidence |
|---|---:|---|
| Flagship HVE Core collection size | 41 artifacts for RPI workflows | [F13:55-60] |
| Repository-wide artifact counts | 49 agents, 102 instructions, 63 prompts, 11 skills | [F13:70-78] |
| Research-capability core artifacts read | 1 prompt, 2 agents, 1 orchestrator, 1 planner, 5 RPI docs, 2 instruction files, 1 collection manifest, 1 root instruction file | Sources section |
| Chatmode artifacts found | 0 `.chatmode.md` matches in repository searches | [F18] |
| Primary research output path in current agent | `.copilot-tracking/research/{{YYYY-MM-DD}}/<topic>-research.md` | [F02:80-87], [F02:308-312] |
| Subagent output path | `.copilot-tracking/research/subagents/{{YYYY-MM-DD}}/<topic>.md` or `<topic>-research.md` depending context | [F03:14-18], [F04:131-139] |

## 2. RPI overview and where Research sits

hve-core describes HVE Core as a library of specialized agents, auto-applied instructions, reusable prompts, and skills for GitHub Copilot [F13:27-28]. Its flagship extension installs a collection for RPI, described as Research to Plan to Implement workflows [F13:55-60]. The `hve-core.collection.yml` manifest shows the older composition pattern: separate `kind: agent` entries for `rpi-agent`, `task-researcher`, `task-planner`, `task-implementor`, and `task-reviewer`, plus a `researcher-subagent`, and prompts such as `rpi.prompt.md`, `task-research.prompt.md`, and `task-plan.prompt.md` [F14:16-63].

The dedicated RPI orchestrator frames work as a five-phase loop: Research, Plan, Implement, Review, Discover [F04:36-39]. Research is Phase 1. The orchestrator assesses difficulty and decides whether to keep research in context or escalate to durable artifacts and subagents [F04:51-62], [F04:217-226]. That means hve-core has two Research modes:

| Mode | When used | Output |
|---|---|---|
| Lightweight in-context research | Simple or medium tasks | Refined request ordering, assumptions, and findings remain in working context [F04:253-258] |
| Document-backed research | Medium-hard or challenging tasks, or tasks upgraded after investigation | Primary research document plus optional subagent research outputs [F04:119-139], [F04:253-266] |

The standalone Task Researcher is heavier than RPI Agent's built-in Phase 1. It is a research-only specialist that always produces a single authoritative document in `.copilot-tracking/research/` [F02:17-20]. User-facing docs recommend it for multi-file changes, unfamiliar frameworks, external APIs, unclear requirements, and architecture decisions [F10:18-27].

## 3. Research phase mechanics

### 3.1 Trigger and routing

The primary slash-command style prompt is `.github/prompts/hve-core/task-research.prompt.md`. Its frontmatter declares `agent: Task Researcher` and an argument hint of `topic=... [chat={true|false}]` [F01:1-5]. The prompt inputs are a required topic and optional chat context; requirements include incorporating conversation context, scoping related files/patterns/external references, evaluating alternatives, selecting a recommendation, and producing a consolidated research document [F01:9-19].

User documentation describes two entry points: type `/task-research <topic>` or manually select the Task Researcher custom agent [F10:55-73]. The full workflow guide shows `/task-research` as Phase 1 and expects a research document before Phase 2 planning [F12:53-81].

Good: `/task-research Azure Blob Storage integration for Python data pipeline` with concrete questions and constraints.

Bad: Asking Task Researcher to implement code, skipping research for complex work, or providing vague descriptions; the guide explicitly warns against those pitfalls [F10:113-134].

### 3.2 Agent instructions and responsibilities

The Task Researcher agent frontmatter defines it as disabled for direct model invocation, lists `Researcher Subagent`, and defines handoffs to Task Planner and deeper research [F02:1-15]. Its core principles enforce a hard boundary: create/edit only under `.copilot-tracking/research/`, document verified findings from actual tools, follow repository conventions, prefer one recommended approach, and author with implementation in mind including examples, file references, line numbers, and pitfalls [F02:21-31].

The most important architectural rule is delegation: Task Researcher delegates all research to Researcher Subagent. Direct execution is limited to creating/updating research files, synthesizing subagent outputs, and communicating findings [F02:33-35]. This is stronger than a mere suggestion; operational constraints later repeat that all research tool usage goes to subagents, while the parent may read/write research files directly and may never modify files outside `.copilot-tracking/research/` [F02:302-307].

### 3.3 Decomposition and parallelism

Task Researcher explicitly instructs the parent to run `Researcher Subagent` with `runSubagent` or `task`, providing research topics/questions and a subagent output path [F02:37-42]. Independent topics can be parallelized [F02:44-48]. Its required phases are:

| Phase | Activity | Evidence |
|---|---|---|
| 1. Research | Define scope/questions/risks; prepare primary document; run subagents; consolidate findings | [F02:110-139] |
| 2. Analysis and Completion | Evaluate alternatives; run subagents for alternatives if needed; select one approach; update examples/citations/implementation details | [F02:141-165] |

The Researcher Subagent is intentionally narrower. It researches specific questions using search, read, web, GitHub repo, and MCP tools [F03:10-18]. It writes progressive findings, references/evidence, follow-on questions, key discoveries, and clarifying questions to a subagent file [F03:19-29]. It stops when original questions are answered and avoids tangential threads [F03:41-45]. The chat response is deliberately compressed; full detail remains on disk [F03:62-74].

### 3.4 Citation and evidence discipline

Evidence is a first-class success criterion. Task Researcher considers research complete only when the dated file contains scope, an evidence log with sources, evaluated alternatives, complete examples and line-number references, and actionable next steps [F02:97-106]. Technical scenario analysis requires runnable examples and exact references as paths with line ranges [F02:167-175]. User docs say the researcher documents findings with evidence, sources, and line references, and recommends one approach [F10:28-34]. The getting-started guide describes this as verified truth: the agent searched, read, and cited rather than guessing [F11:45-59].

A notable convention: workspace file references inside `.copilot-tracking/` should be plain-text paths, not markdown links or `#file:` directives, because VS Code may resolve missing targets and create Problems noise [F02:177-185], [F03:52-60]. External URLs may use markdown links [F02:185-186].

## 4. Research output artifact and structure

The current Task Researcher declares two locations:

| Artifact | Location | Purpose |
|---|---|---|
| Primary research document | `.copilot-tracking/research/{{YYYY-MM-DD}}/task-description-research.md` | Consolidated evidence, alternatives, recommendation, examples [F02:80-87], [F02:308-312] |
| Subagent research outputs | `.copilot-tracking/research/subagents/{{YYYY-MM-DD}}/topic-research.md` | Raw/focused subagent findings, sources, next topics, clarifying questions [F02:80-87], [F04:131-139] |

The research document template embedded in Task Researcher is detailed and implementation-oriented. Required sections include Task Implementation Requests, Scope and Success Criteria, Outline, Potential Next Research, Research Executed with File Analysis, Code Search Results, External Research, Project Conventions, Key Discoveries, Complete Examples, API and Schema Documentation, Configuration Examples, and Technical Scenarios with requirements, preferred approach, implementation details, and considered alternatives [F02:187-300]. The file must start with `<!-- markdownlint-disable-file -->`, because `.copilot-tracking/**` files are exempt from linter rules [F02:97-108].

There is a minor documentation discrepancy: the user-facing Task Researcher guide shows a flat path `.copilot-tracking/research/{{YYYY-MM-DD}}-<topic>-research.md` [F10:39-45], while the current agent instructions use a dated directory `.copilot-tracking/research/{{YYYY-MM-DD}}/<topic>-research.md` [F02:80-87]. For portability, prefer the agent's dated directory because it is the executable behavior and matches the RPI Agent tracking artifact model [F04:119-139].

## 5. Storage, naming, and supporting artifact conventions

The root Copilot instructions define `.copilot-tracking/` as the gitignored home for AI workflow artifacts, including research, plans, details, changes, reviews, ADR drafts, and other session state [F15:86-107]. The RPI full workflow docs summarize the same convention: Research lives under `.copilot-tracking/research/`, Plan under `.copilot-tracking/plans/`, Details under `.copilot-tracking/details/`, Changes under `.copilot-tracking/changes/`, and Review under `.copilot-tracking/reviews/` [F12:233-244].

Naming rules in Task Researcher are simple: use the current date for new research; retain the existing date when extending a file; name research documents `task-or-topic-description-research.md` under `.copilot-tracking/research/{{YYYY-MM-DD}}/` [F02:308-312].

No `.chatmode.md` files were found by repository searches for `filename:*.chatmode.md` and `path:.github chatmode` [F18]. The relevant persona and routing layer is therefore `.agent.md` plus `.prompt.md`, not chat modes, for the RPI Research capability.

## 6. Research to Plan handoff

The handoff is both UI-level and artifact-level.

At UI level, Task Researcher frontmatter defines a handoff labelled Create Plan to `agent: Task Planner` with prompt `/task-plan` and `send: true` [F02:7-11]. The user guide also advises using the Create Plan handoff button when available [F10:136-147].

At artifact level, Task Researcher ends with explicit Ready for Planning instructions: clear context, attach/open the research document, and start planning with `/task-plan` [F02:343-347]. The docs explain why context clearing matters: each agent has different instructions, research findings are in files rather than chat history, and clean context lets each agent work optimally [F12:36-47].

Task Planner consumes research directly. Its prompt accepts optional `research=...`; if absent, it checks `.copilot-tracking/research/` for relevant files [F06:1-18]. The planner's core principles require plans to be grounded in verified research and to track discrepancies between research recommendations and planned implementation [F05:17-29]. In Phase 1, it locates existing research, checks whether it covers requirements, creates a lightweight research file if none exists, or runs Researcher Subagent to fill gaps [F05:158-180]. During plan creation it populates unaddressed research items and plan deviations from research in the Planning Log [F05:193-201]. Later templates trace success criteria and discrepancies to research line ranges [F05:408-422], [F05:537-549]. User docs state the chain explicitly: Plan to Details line references to Research line references [F09:141-147].

This is the key design pattern worth adopting: Research does not pass a chat summary to Plan. It passes a durable, cited artifact that Plan can re-read, cross-reference, and challenge.

## 7. Supporting artifacts: prompts, instructions, collections, docs

| Artifact type | hve-core artifact | Role in Research capability |
|---|---|---|
| Prompt | `.github/prompts/hve-core/task-research.prompt.md` | Slash-command trigger and minimal input contract [F01] |
| Main agent | `.github/agents/hve-core/task-researcher.agent.md` | Research-only persona, workflow, output template, handoff [F02] |
| Subagent | `.github/agents/hve-core/subagents/researcher-subagent.agent.md` | Focused research worker and evidence file writer [F03] |
| Orchestrator | `.github/agents/hve-core/rpi-agent.agent.md` | Lightweight-vs-durable decision, phase integration [F04] |
| Planner | `.github/agents/hve-core/task-planner.agent.md` | Research consumer and gap-filling behavior [F05] |
| Plan prompt | `.github/prompts/hve-core/task-plan.prompt.md` | Handoff command accepts optional research path [F06] |
| Collection manifest | `collections/hve-core.collection.yml` | Bundles agents, subagents, and prompts together [F14] |
| Root instructions | `.github/copilot-instructions.md` | Defines `.copilot-tracking/` and agent/subagent conventions [F15] |
| Design Thinking overlay | `dt-rpi-research-context.instructions.md` | Augments standard research when Design Thinking handoff artifacts are in scope [F16] |

The Design Thinking research context is relevant as an example of extension by instruction file. It changes framing from purely technical feasibility to stakeholder impact plus feasibility, marks assumptions as verification targets, adds stakeholder impact and gap-resolution sections, and can recommend returning to DT coaching rather than proceeding to planning [F16:10-61]. This is portable as optional project rules or an extension action, not as core Research mechanics.

## Portability to a skill/action/rule/template model

### Concrete mapping

| hve-core source concept | Skill-model equivalent | Portability judgment |
|---|---|---|
| `.github/prompts/hve-core/task-research.prompt.md` | Skill invocation keywords/slash command plus `actions/research.md` input contract | Adopt. Keep the concise trigger contract. |
| `Task Researcher` agent persona | `SKILL.md` capability entry and `actions/research.md` execution flow | Adopt, but remove UI-specific handoff metadata. |
| `Researcher Subagent` | Optional sub-action or rule for delegation; if available, launch parallel research agents; otherwise perform direct research | Adopt with fallback. Do not require one specific tool name. |
| Embedded Research Document Template | `templates/research.md` | Adopt strongly. Make standalone and work-package variants explicit. |
| Operational constraints | `rules/research.md` immutable rules | Adopt. Rules should enforce evidence, citation, scope, output paths, no implementation edits. |
| File reference formatting | `rules/citations.md` or part of research rule | Adopt. Plain paths for local workspace refs, markdown links for external URLs. |
| Research to Plan handoff | `actions/research.md` completion plus optional `actions/plan-handoff.md` | Adopt. Emit durable artifact path and next-step instructions. |
| `.copilot-tracking/` storage | `project/research/<dated-slug>/` or `project/work/<wp>/research/` | Adapt. Use target repo's project artifact layout, not hve-core's private tracking folder. |
| `.agent.md` handoffs and emoji response headings | Skill registry and action outputs | Avoid as legacy/UI baggage. |
| Design Thinking overlay instructions | Optional project rule or specialized extension skill | Adopt selectively; not part of core research capability. |

### Recommended target skill shape

```text
.agents/skills/research/
├── SKILL.md
├── actions/
│   ├── research.md
│   └── handoff-to-plan.md
├── rules/
│   ├── rules.index.md
│   ├── research.md
│   ├── citations.md
│   └── output-location.md
└── templates/
    ├── standalone-research.md
    └── work-package-research.md
```

Good: `actions/research.md` decides output location from context: standalone research goes to `project/research/<dated-slug>/`; work-package research goes to `project/work/<wp>/research/`.

Bad: Hard-code `.copilot-tracking/research/` into the skill. That path is hve-core-specific and mostly intended as gitignored local session state [F15:86-107].

### What to adopt

1. Artifact-first handoff. The research output should be the canonical source consumed by Plan, not a chat transcript.
2. Evidence-linked findings. Require file paths with line ranges and external URLs for claims.
3. Explicit alternatives analysis. Require at least selected approach, rejected alternatives, rationale, and risks.
4. Progressive decomposition. Allow parallel subagents for independent research questions, but synthesize into one primary artifact.
5. Gap tracking. Preserve Potential Next Research, unresolved questions, assumptions, and planning-impact notes.
6. Plan-readiness criteria. Research is complete only when enough information exists to define implementation sequence and success criteria.

### What to avoid

1. UI-specific handoff frontmatter as the core protocol. Use skill action outputs instead.
2. Mandatory subagent-only execution. hve-core can require `runSubagent`/`task`; a portable skill should gracefully fall back to direct research if subagents are unavailable.
3. Conflicting path conventions. hve-core docs contain both flat and dated-directory examples [F10:39-45], [F02:80-87]. Pick one target convention and enforce it.
4. Emoji-dependent response formats. The target dossier and project conventions should remain readable in plain terminals.
5. Treating Design Thinking overlays as required. They are valuable optional context, not the core RPI Research phase.

### Standalone versus work-package output convention

For the target repository, the hve-core handoff pattern supports two output modes:

| Mode | Recommended target path | When to use | Plan consumption |
|---|---|---|---|
| Standalone research | `project/research/<YYYY-MM-DD>-<slug>/<YYYY-MM-DD>-<slug>-research.md` | Exploratory research not yet tied to a work package | Plan action can cite standalone research and later copy/link it into a work package if work is created |
| Work-package research | `project/work/<wp>/research/<YYYY-MM-DD>-<slug>-research.md` | Research for an active issue/work package | Plan action consumes it directly from the work package and records line-referenced requirements |

The key is not the exact path; it is the durable, cited document plus a predictable handoff rule. hve-core proves that Plan can be much more reliable when it starts by locating research, checking coverage, filling gaps, and recording deviations rather than improvising from conversation history [F05:158-180], [F05:537-549].

## Sources

| ID | Source read | URL |
|---|---|---|
| F01 | `.github/prompts/hve-core/task-research.prompt.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/prompts/hve-core/task-research.prompt.md |
| F02 | `.github/agents/hve-core/task-researcher.agent.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/agents/hve-core/task-researcher.agent.md |
| F03 | `.github/agents/hve-core/subagents/researcher-subagent.agent.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/agents/hve-core/subagents/researcher-subagent.agent.md |
| F04 | `.github/agents/hve-core/rpi-agent.agent.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/agents/hve-core/rpi-agent.agent.md |
| F05 | `.github/agents/hve-core/task-planner.agent.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/agents/hve-core/task-planner.agent.md |
| F06 | `.github/prompts/hve-core/task-plan.prompt.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/prompts/hve-core/task-plan.prompt.md |
| F07 | `.github/prompts/hve-core/rpi.prompt.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/prompts/hve-core/rpi.prompt.md |
| F08 | `docs/rpi/task-planner.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/docs/rpi/task-planner.md |
| F09 | `docs/rpi/task-planner.md`, line-reference section | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/docs/rpi/task-planner.md |
| F10 | `docs/rpi/task-researcher.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/docs/rpi/task-researcher.md |
| F11 | `docs/getting-started/first-research.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/docs/getting-started/first-research.md |
| F12 | `docs/rpi/using-together.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/docs/rpi/using-together.md |
| F13 | `README.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/README.md |
| F14 | `collections/hve-core.collection.yml` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/collections/hve-core.collection.yml |
| F15 | `.github/copilot-instructions.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/copilot-instructions.md |
| F16 | `.github/instructions/design-thinking/dt-rpi-research-context.instructions.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/instructions/design-thinking/dt-rpi-research-context.instructions.md |
| F17 | `.github/instructions/design-thinking/dt-method-02-research.instructions.md` | https://github.com/microsoft/hve-core/blob/d2868d5a528025edc91b39f912e210ed391fd6c1/.github/instructions/design-thinking/dt-method-02-research.instructions.md |
| F18 | GitHub code search queries: `repo:microsoft/hve-core filename:*.chatmode.md`, `repo:microsoft/hve-core path:.github chatmode`; both returned 0 results | https://github.com/microsoft/hve-core/search?q=filename%3A%2A.chatmode.md&type=code |
