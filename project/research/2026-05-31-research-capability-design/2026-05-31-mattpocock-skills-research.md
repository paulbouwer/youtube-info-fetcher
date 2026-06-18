# Research Dossier: mattpocock/skills research-capability patterns

| Field | Value |
|---|---|
| Generated date | 2026-05-31 |
| Research Query | Reference #2: identify research-related skills and skill-authoring conventions in `https://github.com/mattpocock/skills` |
| Mode | Standalone research |
| Scope | Public repository `mattpocock/skills` at observed ref `e3b90b5238f38cdea5996e16861dcae28ef52eda`; stable, in-progress, personal, misc, and deprecated skill buckets were considered. |
| Method | Listed repository root and skill buckets, enumerated every `SKILL.md`, read relevant skill manifests and bodies, read authoring/setup docs and related templates/reference files, then contrasted the repo's minimalist prompt-skill style with the target system's structured skill/action/rule/template model. |

## 1. Executive Summary

This dossier answers: what in `mattpocock/skills` is relevant to research, investigation, information-gathering, interrogation, planning-from-research, or producing analysis artifacts, and what skill conventions are worth borrowing for a research capability.

Headline finding: there is no stable skill named `research`, but the repo has a family of research-adjacent skills that encode useful research behaviors: relentless questioning, codebase exploration, diagnosis through feedback loops, architectural investigation, issue/PRD synthesis, review by parallel subagents, and artifact production [F01] [F02] [F05] [F07] [F08] [F09] [F10] [F11] [F12].

Key insights:

1. **The house style is intentionally small and composable.** The top-level README says the skills are "small, easy to adapt, and composable," explicitly positioning them against heavier process frameworks [F01].
2. **Most skills are one-file prompt programs.** A typical skill is a folder containing `SKILL.md` with YAML frontmatter and direct imperative instructions. Supporting files appear only when needed for reference formats, templates, or setup seeds [F21] [F24] [F27] [F30].
3. **Research is usually embedded inside a task, not separated.** Skills say "explore the repo," "read the glossary," "respect ADRs," "attempt reproduction," or "use an Explore subagent" as steps inside a workflow rather than invoking a standalone research phase [F07] [F09] [F10] [F11] [F12].
4. **Questioning is treated as alignment infrastructure.** `grill-me` and `grill-with-docs` turn plans into decision trees and force one-question-at-a-time clarification; the docs variant also updates `CONTEXT.md` and ADRs inline [F05] [F06].
5. **Parallelism is rare and reserved for divergent analysis.** Only a few skills explicitly call for subagents: `improve-codebase-architecture` uses an Explore subagent, deprecated `design-an-interface` spawns 3+ design subagents, and in-progress `review` runs Standards and Spec reviews in parallel [F08] [F16] [F17].
6. **Artifact discipline is strong but lightweight.** Skills use embedded templates for PRDs, issues, QA reports, triage notes, agent briefs, ADRs, context glossaries, HTML architecture reports, handoffs, and writing fragments [F09] [F10] [F11] [F12] [F14] [F15] [F20] [F21] [F23] [F24].

Quick stats:

| Metric | Finding |
|---|---:|
| `SKILL.md` files enumerated | 29 [F33] |
| Stable plugin skills | 14 [F30] |
| Stable research-adjacent skills | 10 |
| In-progress/deprecated/personal research-adjacent skills | 9 |
| Dedicated stable `research` skill | 0 |
| Skills with explicit parallel/subagent instructions | 3 verified: `improve-codebase-architecture`, `design-an-interface`, `review` [F08] [F16] [F17] |
| General skill structure | `skill-name/SKILL.md`; optional `REFERENCE.md`, `EXAMPLES.md`, `scripts/` [F21] |
| Common manifest fields observed | `name`, `description`; sometimes `disable-model-invocation`, `argument-hint` [F07] [F13] [F14] [F18] |

## 2. Inventory of research-related skills

The table includes every skill I found that relates directly or adjacently to research, investigation, information-gathering, interrogation, planning-from-research, or producing analysis artifacts. It also distinguishes stable skills from in-progress, deprecated, and personal ones.

| Skill | Path | Status/bucket | Research relevance | Artifact/output | Subagents/parallelism |
|---|---|---|---|---|---|
| `grill-me` | `skills/productivity/grill-me/SKILL.md` | Stable productivity | Interrogates a plan/design until shared understanding; explores code instead of asking when code can answer [F05]. | In-context Q&A only. | None specified. |
| `grill-with-docs` | `skills/engineering/grill-with-docs/SKILL.md` | Stable engineering | Same interrogation loop, but grounded in domain glossary, ADRs, code contradictions, and scenarios [F06]. | Updates `CONTEXT.md`; offers ADRs using bundled formats [F06] [F23] [F24]. | None specified. |
| `diagnose` | `skills/engineering/diagnose/SKILL.md` | Stable engineering | Disciplined bug/performance investigation: feedback loop, reproduce, hypotheses, instrumentation, fix, post-mortem [F07]. | Failing tests, harnesses, logs, regression tests, post-mortem notes. | No subagents specified. |
| `improve-codebase-architecture` | `skills/engineering/improve-codebase-architecture/SKILL.md` | Stable engineering | Researches architectural friction and deepening opportunities via glossary, ADRs, Explore subagent, and visual reporting [F08]. | Self-contained HTML report in OS temp dir; possible `CONTEXT.md` and ADR updates later [F08] [F25] [F26]. | Uses `Agent` tool with `subagent_type=Explore` [F08]. |
| `zoom-out` | `skills/engineering/zoom-out/SKILL.md` | Stable engineering | Requests a higher-level map of relevant modules/callers when user does not know a code area [F13]. | In-context map. | None specified. |
| `to-prd` | `skills/engineering/to-prd/SKILL.md` | Stable engineering | Synthesizes current conversation plus codebase understanding into a PRD; includes module sketching and test decisions [F09]. | PRD issue with embedded template [F09]. | None specified. |
| `to-issues` | `skills/engineering/to-issues/SKILL.md` | Stable engineering | Gathers context from plans/specs/issues and optionally explores code before slicing work [F10]. | Issue tracker issues using embedded issue template [F10]. | None specified. |
| `triage` | `skills/engineering/triage/SKILL.md` | Stable engineering | Reads issue history, labels, comments, out-of-scope knowledge, codebase, and reproduction attempts to classify issues [F11]. | Triage comments, agent briefs, needs-info notes, `.out-of-scope/` records [F11] [F22]. | No parallelism specified, but may invoke `grill-with-docs` [F11]. |
| `prototype` | `skills/engineering/prototype/SKILL.md` | Stable engineering | Uses throwaway code to answer design questions and sanity-check state models/UI options [F19]. | Prototype code plus durable captured answer in commit/ADR/issue/notes [F19]. | No subagents specified. |
| `handoff` | `skills/productivity/handoff/SKILL.md` | Stable productivity | Summarizes current conversation for future agents, preserving research context without duplicating artifacts [F14]. | Handoff document in OS temp dir [F14]. | None specified. |
| `write-a-skill` | `skills/productivity/write-a-skill/SKILL.md` | Stable productivity | Not research itself, but captures authoring conventions needed to port a research skill [F21]. | `SKILL.md`, optional references/examples/scripts [F21]. | None specified. |
| `review` | `skills/in-progress/review/SKILL.md` | In progress | Analyzes a diff against standards and spec sources, intentionally separating two research axes [F17]. | Aggregated review report. | Runs Standards and Spec reviews in parallel subagents [F17]. |
| `teach` | `skills/in-progress/teach/SKILL.md` | In progress | Knowledge acquisition from trusted resources; builds mission, glossary, resources, records, explainers, exercises [F18]. | `MISSION.md`, `GLOSSARY.md`, `RESOURCES.md`, `learning-records/*.md`, HTML explainers [F18]. | None specified. |
| `writing-fragments` | `skills/in-progress/writing-fragments/SKILL.md` | In progress | Grilling session that mines the user for raw observations/fragments before imposing structure [F27]. | Appends fragments to a markdown file [F27]. | None specified. |
| `writing-shape` | `skills/in-progress/writing-shape/SKILL.md` | In progress | Reads raw material end-to-end, argues structure, and shapes analysis into an article [F28]. | Separate article document [F28]. | None specified. |
| `writing-beats` | `skills/in-progress/writing-beats/SKILL.md` | In progress | Converts raw material into a beat-by-beat narrative journey [F29]. | Article file, appended one beat at a time [F29]. | None specified. |
| `qa` | `skills/deprecated/qa/SKILL.md` | Deprecated | Conversational bug-report intake; clarifies, explores codebase in background, scopes issue breakdowns [F15]. | GitHub issues with templates [F15]. | Uses background Explore subagent while talking to user [F15]. |
| `request-refactor-plan` | `skills/deprecated/request-refactor-plan/SKILL.md` | Deprecated | Interviews user, verifies assertions in repo, explores options, checks tests, then creates a refactor plan [F12]. | GitHub issue with refactor-plan template [F12]. | None specified. |
| `ubiquitous-language` | `skills/deprecated/ubiquitous-language/SKILL.md` | Deprecated | Extracts glossary from conversation, flags ambiguity, proposes canonical domain terms [F20]. | `UBIQUITOUS_LANGUAGE.md` [F20]. | None specified. |
| `design-an-interface` | `skills/deprecated/design-an-interface/SKILL.md` | Deprecated | Explores multiple interface designs and compares trade-offs [F16]. | Comparative in-context design analysis. | Spawns 3+ simultaneous subagents with different constraints [F16]. |
| `obsidian-vault` | `skills/personal/obsidian-vault/SKILL.md` | Personal | Personal note search/create/manage workflow for an AI Research Obsidian vault [F31]. | Obsidian notes and index notes. | None specified. |

## 3. Deep dives on relevant skills

### 3.1 `grill-me`: pure interrogation as research

Manifest:

```yaml
name: grill-me
description: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree. Use when user wants to stress-test a plan, get grilled on their design, or mentions "grill me".
```

No extra metadata or attribution fields were present in the manifest [F05].

How it works: the body is only a few lines, but it is forceful. It tells the agent to interview relentlessly, walk the design tree branch by branch, resolve dependencies between decisions, provide a recommended answer for each question, and ask questions one at a time [F05]. It also contains a critical research instruction: if a question can be answered by exploring the codebase, the agent should explore the codebase instead of asking the user [F05].

Use pattern: this is a pure in-context prompt. It does not define templates, output files, subagents, or phases. Its value is behavioral: it changes the agent from answerer to interrogator.

Good: one-question-at-a-time decision-tree grilling for ambiguous plans.

Bad: using it to produce a permanent research dossier without adding an artifact rule; the skill itself does not require citations or file output.

### 3.2 `grill-with-docs`: interrogation plus durable domain memory

Manifest:

```yaml
name: grill-with-docs
description: Grilling session that challenges your plan against the existing domain model, sharpens terminology, and updates documentation (CONTEXT.md, ADRs) inline as decisions crystallise. Use when user wants to stress-test a plan against their project's language and documented decisions.
```

No attribution fields were present [F06].

How it works: the top-level behavior mirrors `grill-me`: interview relentlessly, walk the design tree, ask one question at a time, and explore code instead of asking when code can answer [F06]. The difference is `supporting-info`: during exploration the agent looks for `CONTEXT.md`, `CONTEXT-MAP.md`, and `docs/adr/`; challenges conflicts with existing language; sharpens overloaded terms; invents concrete scenarios; checks claims against code; updates `CONTEXT.md` immediately; and offers ADRs sparingly only for hard-to-reverse, surprising, trade-off decisions [F06].

Supporting files: `CONTEXT-FORMAT.md` defines the glossary format and single-vs-multi-context convention [F23]. `ADR-FORMAT.md` defines extremely short ADRs in `docs/adr/0001-slug.md` style and repeats the three-part ADR qualification test [F24].

Use pattern: mostly in-context, but with controlled side effects into documentation. This is the closest stable pattern to "research that improves future agent context."

Good: inline capture of resolved terminology while the conversation is still fresh.

Bad: batching terminology decisions until the end; the skill explicitly says to update `CONTEXT.md` as terms are resolved [F06].

### 3.3 `diagnose`: investigation through a feedback-loop discipline

Manifest:

```yaml
name: diagnose
description: Disciplined diagnosis loop for hard bugs and performance regressions. Reproduce → minimise → hypothesise → instrument → fix → regression-test. Use when user says "diagnose this" / "debug this", reports a bug, says something is broken/throwing/failing, or describes a performance regression.
```

No attribution fields were present [F07].

How it works: the skill is a structured investigative method. Phase 1 is explicitly called "the skill": build a fast, deterministic, agent-runnable pass/fail signal. It lists concrete feedback-loop options, including failing tests, curl scripts, CLI fixtures, browser scripts, trace replay, throwaway harnesses, property/fuzz loops, bisection harnesses, differential loops, and HITL scripts [F07]. Later phases reproduce the bug, generate 3-5 ranked falsifiable hypotheses, instrument one variable at a time, write regression tests where a correct seam exists, clean debug logs, and capture the correct hypothesis in the commit/PR message [F07].

Use pattern: no subagents or external templates are required. The artifact is the feedback loop and regression test, not a report. It is research-by-experiment.

Good: "If X is the cause, changing Y will make the bug disappear" hypothesis format [F07].

Bad: hypothesizing without a loop; the skill says not to proceed if no loop exists [F07].

### 3.4 `improve-codebase-architecture`: exploratory codebase research plus visual analysis artifact

Manifest:

```yaml
name: improve-codebase-architecture
description: Find deepening opportunities in a codebase, informed by the domain language in CONTEXT.md and the decisions in docs/adr/. Use when the user wants to improve architecture, find refactoring opportunities, consolidate tightly-coupled modules, or make a codebase more testable and AI-navigable.
```

No attribution fields were present [F08].

How it works: the skill defines a vocabulary for architectural research: module, interface, implementation, depth, seam, adapter, leverage, locality [F08] [F26]. It first reads the domain glossary and relevant ADRs, then uses an Explore subagent to walk the codebase organically and note friction: concept-hopping, shallow modules, testability seams, tight coupling, untested areas, and deletion-test signals [F08].

Artifact: it writes a self-contained HTML report to the OS temp directory, opens it for the user, and includes visual before/after cards for each candidate with files, problem, solution, benefits, diagrams, and recommendation strength [F08]. The bundled `HTML-REPORT.md` gives a detailed scaffold, Tailwind/Mermaid guidance, diagram patterns, and terse editorial style [F25].

Later phase: once the user chooses a candidate, the skill enters a grilling loop and may update `CONTEXT.md`, offer ADRs, or use interface-design guidance [F08].

Use pattern: this is the most complete research/analysis artifact pattern in the stable set. It combines codebase exploration, constrained vocabulary, visual report output, and follow-up interrogation.

Portability note: the skill says to write to OS temp; the target system's research dossiers appear to prefer durable project paths. Borrow the report structure, not necessarily the temp-file location.

### 3.5 `zoom-out`: micro research prompt for orientation

Manifest:

```yaml
name: zoom-out
description: Tell the agent to zoom out and give broader context or a higher-level perspective. Use when you're unfamiliar with a section of code or need to understand how it fits into the bigger picture.
disable-model-invocation: true
```

`disable-model-invocation: true` is an additional manifest key observed here [F13].

How it works: the body is a single user-voice prompt: "I don't know this area of code well. Go up a layer of abstraction. Give me a map of all the relevant modules and callers, using the project's domain glossary vocabulary" [F13].

Use pattern: pure in-context orientation. No artifact, no template, no subagent instruction. It is useful as a sub-action inside a research skill: produce a map before deep investigation.

### 3.6 `to-prd`: planning-from-research synthesis

Manifest:

```yaml
name: to-prd
description: Turn the current conversation context into a PRD and publish it to the project issue tracker. Use when user wants to create a PRD from the current context.
```

No attribution fields were present [F09].

How it works: the skill explicitly does not interview the user; it synthesizes the current conversation and codebase understanding. It explores the repo if needed, uses domain glossary vocabulary, respects ADRs, sketches modules to build or modify, looks for deep modules, checks module/test expectations with the user, then writes and publishes a PRD [F09].

Artifact: embedded PRD template with Problem Statement, Solution, extensive User Stories, Implementation Decisions, Testing Decisions, Out of Scope, and Further Notes [F09].

Use pattern: planning-from-existing-research rather than primary research. It is a useful downstream consumer of a research dossier.

### 3.7 `to-issues`: research-to-execution slicing

Manifest:

```yaml
name: to-issues
description: Break a plan, spec, or PRD into independently-grabbable issues on the project issue tracker using tracer-bullet vertical slices. Use when user wants to convert a plan into issues, create implementation tickets, or break down work into issues.
```

No attribution fields were present [F10].

How it works: the skill gathers context from conversation or an issue reference, optionally explores the codebase, drafts tracer-bullet vertical slices, quizzes the user on granularity/dependencies/HITL-vs-AFK classification, then publishes issues in dependency order [F10].

Artifact: embedded issue template with Parent, What to build, Acceptance criteria, and Blocked by [F10].

Use pattern: not a research skill by itself, but a strong conversion pattern from research output to executable work.

### 3.8 `triage`: issue investigation and agent-brief production

Manifest:

```yaml
name: triage
description: Triage issues through a state machine driven by triage roles. Use when user wants to create an issue, triage issues, review incoming bugs or feature requests, prepare issues for an AFK agent, or manage issue workflow.
```

No attribution fields were present [F11].

How it works: the skill defines category and state roles, then for a specific issue gathers full issue context, labels, reporter, dates, comments, prior triage notes, codebase context, domain glossary vocabulary, ADRs, and `.out-of-scope` knowledge [F11]. For bugs, it attempts reproduction before grilling. If the issue needs more detail, it invokes `/grill-with-docs` [F11].

Artifacts: triage comments must include an AI disclaimer; `ready-for-agent` results in an agent brief; `needs-info` produces triage notes; `wontfix` enhancements write to `.out-of-scope/` [F11]. The bundled `AGENT-BRIEF.md` emphasizes durable, behavioral, non-procedural briefs with acceptance criteria and out-of-scope boundaries [F22].

Use pattern: excellent for research-to-delegation. It is less about deep literature/code research and more about turning messy reports into durable agent contracts.

### 3.9 `prototype`: research by throwaway experiment

Manifest:

```yaml
name: prototype
description: Build a throwaway prototype to flesh out a design before committing to it. Routes between two branches — a runnable terminal app for state/business-logic questions, or several radically different UI variations toggleable from one route. Use when the user wants to prototype, sanity-check a data model or state machine, mock up a UI, explore design options, or says "prototype this", "let me play with it", "try a few designs".
```

No attribution fields were present [F19].

How it works: a prototype is defined as throwaway code that answers a question. The skill chooses between logic/state-model and UI branches, marks code as throwaway, requires one command to run, avoids persistence by default, surfaces state, and deletes or absorbs the prototype when done [F19].

Artifact: the prototype is transient; the only durable output should be the answer captured in a commit message, ADR, issue, or `NOTES.md` near the prototype [F19].

Use pattern: valuable for empirical design research where reading code/docs is insufficient.

### 3.10 `handoff`: compacting research context

Manifest:

```yaml
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
```

`argument-hint` is an additional manifest key observed here [F14].

How it works: the skill writes a handoff document, includes suggested skills, references existing artifacts instead of duplicating them, and redacts sensitive information [F14].

Use pattern: this is not primary research, but it is a context-preservation pattern. A research skill can borrow its rule: reference durable artifacts by path/URL instead of copying them.

### 3.11 `review`: parallel two-axis analysis

Manifest:

```yaml
name: review
description: Review the changes since a fixed point (commit, branch, tag, or merge-base) along two axes — Standards (does the code follow this repo's documented coding standards?) and Spec (does the code match what the originating issue/PRD asked for?). Runs both reviews in parallel sub-agents and reports them side by side. Use when the user wants to review a branch, a PR, work-in-progress changes, or asks to "review since X".
```

No attribution fields were present [F17].

How it works: the skill pins a fixed point, identifies the spec source, identifies standards sources, then spawns two `general-purpose` subagents in parallel: Standards and Spec [F17]. It gives each subagent a concise brief and aggregates the reports under separate headings without merging or reranking [F17].

Use pattern: highly portable to research. It demonstrates when to use parallelism: not for speed alone, but to keep analytical axes independent so one does not pollute the other.

### 3.12 Deprecated and in-progress idea-gathering/artifact skills

These are not stable plugin skills but contain useful research patterns:

| Skill | Pattern worth noting |
|---|---|
| `qa` | Conversational intake with limited clarifying questions; background Explore subagent for context; issue bodies avoid file paths and use domain language [F15]. |
| `request-refactor-plan` | Ask for detailed problem description, verify assertions in repo, present alternatives, interview thoroughly, scope tiny commits, create issue [F12]. |
| `ubiquitous-language` | Extract domain terms from conversation, flag ambiguity, write a structured glossary with relationships and example dialogue [F20]. |
| `design-an-interface` | Use 3+ parallel subagents to generate radically different designs, then compare interface simplicity, generality, efficiency, depth, and misuse risk [F16]. |
| `teach` | For learning/research, do not trust parametric knowledge; gather high-quality resources, maintain mission/glossary/resources/learning records, and produce HTML explainers/exercises [F18]. |
| `writing-fragments` | Mine raw noticings without imposing structure; append fragments continuously while preserving user edits [F27]. |
| `writing-shape` | Read raw material end-to-end, draft competing openings, grow the article paragraph by paragraph, and argue format choices explicitly [F28]. |
| `writing-beats` | Build a narrative analysis one beat at a time, re-reading the file before each append [F29]. |
| `obsidian-vault` | Personal note workflow for searching/creating/managing an AI Research Obsidian vault; useful only as a personal convention, not generalizable without user-specific path changes [F31]. |

## 4. General skill conventions and philosophy in `mattpocock/skills`

### 4.1 Repository organization

The root `CLAUDE.md` defines bucket folders under `skills/`: `engineering`, `productivity`, `misc`, `personal`, `in-progress`, and `deprecated` [F02]. It also defines publication rules: skills in `engineering`, `productivity`, or `misc` must be referenced in the top-level README and `.claude-plugin/plugin.json`; skills in `personal`, `in-progress`, and `deprecated` must not appear in either [F02]. Bucket README files list every skill in the bucket with one-line descriptions [F03] [F04] [F32].

The top-level README reinforces the product philosophy: real engineering, not vibe coding; small, adaptable, composable skills; and a desire to preserve developer control rather than have a monolithic process own everything [F01].

The plugin manifest lists 14 stable plugin skills, all under `engineering` or `productivity`; no misc skill is currently in the plugin list despite the README reference section listing misc skills [F30] [F01].

### 4.2 Skill folder shape

The authoring skill gives the canonical structure:

```text
skill-name/
├── SKILL.md           # Main instructions (required)
├── REFERENCE.md       # Detailed docs (if needed)
├── EXAMPLES.md        # Usage examples (if needed)
└── scripts/           # Utility scripts (if needed)
    └── helper.js
```

It recommends splitting when `SKILL.md` exceeds 100 lines, when content has distinct domains, or when advanced features are rarely needed [F21]. It recommends scripts only for deterministic operations, repeated generated code, or explicit error handling [F21].

Actual repo practice is slightly broader than the template: support files are often named for their role, such as `CONTEXT-FORMAT.md`, `ADR-FORMAT.md`, `HTML-REPORT.md`, `LANGUAGE.md`, `AGENT-BRIEF.md`, or setup seed files [F23] [F24] [F25] [F26] [F22] [F30].

### 4.3 `SKILL.md` frontmatter shape

The normal manifest shape is minimal YAML:

```yaml
---
name: skill-name
description: Brief description. Use when [specific triggers].
---
```

The `write-a-skill` guidance says the description is the only thing the agent sees when deciding which skill to load, must give enough trigger information, should be max 1024 characters, should be third person, and should use a first sentence for capability plus a second sentence beginning "Use when..." [F21].

Observed optional keys include:

| Key | Example | Meaning inferred from observed files |
|---|---|---|
| `disable-model-invocation` | `zoom-out`, `ubiquitous-language`, `teach` [F13] [F20] [F18] | Marks skills that are phrased as user prompts or stateful behaviors where invocation mechanics may differ. The repo does not define the key in the files read. |
| `argument-hint` | `handoff`, `teach` [F14] [F18] | Provides a slash-command argument hint. |

I found no attribution metadata fields in the relevant `SKILL.md` manifests read. Attribution, when present, is prose: e.g. the README quotes David Thomas, Eric Evans, Kent Beck, and John Ousterhout, and `design-an-interface` references "Design It Twice" from "A Philosophy of Software Design" [F01] [F16].

### 4.4 Naming and body style

Naming uses short kebab-case names that map naturally to slash commands: `grill-me`, `grill-with-docs`, `to-prd`, `to-issues`, `zoom-out`, `write-a-skill` [F01].

Bodies are usually direct imperative instructions rather than abstract documentation. Many include headings like `# Diagnose`, `## Process`, or XML-ish wrappers such as `<what-to-do>` and `<supporting-info>` [F06] [F07] [F27] [F28]. The most minimalist skills are only a few sentences (`grill-me`, `zoom-out`) [F05] [F13]. The more complex skills use embedded templates and reference files (`to-prd`, `to-issues`, `triage`, `improve-codebase-architecture`) [F08] [F09] [F10] [F11].

Good: frontmatter description includes both capability and trigger.

Bad: a vague description like "Helps with documents"; this exact anti-example appears in the authoring guidance [F21].

### 4.5 Relationship to project context

Several engineering skills expect per-repo setup artifacts: issue tracker docs, triage label vocabulary, and domain docs. `setup-matt-pocock-skills` creates this config and includes seed templates for GitHub/GitLab/local issue trackers, domain layout, and triage labels [F30]. `CONTEXT.md` and ADRs then become shared context for `grill-with-docs`, `triage`, `diagnose`, `to-prd`, `to-issues`, `zoom-out`, and `improve-codebase-architecture` [F06] [F07] [F08] [F09] [F10] [F11] [F13].

The repo's `CONTEXT.md` also defines project vocabulary for its own domain: "Issue tracker," "Issue," and "Triage role," and flags resolved ambiguities around "backlog" terminology [F34].

## 5. Parallelism, templates, output files, and in-context prompts

| Pattern | Skills using it | Details |
|---|---|---|
| Pure in-context prompt | `grill-me`, `zoom-out` | Minimal instructions; no output file or template required [F05] [F13]. |
| In-context process with experimental artifacts | `diagnose`, `prototype` | Produces tests/harnesses/prototypes, but the skill is driven by process rather than a reporting template [F07] [F19]. |
| Documentation side effects | `grill-with-docs`, `triage`, `ubiquitous-language`, `teach` | Updates or creates context, ADRs, out-of-scope records, glossaries, learning records [F06] [F11] [F18] [F20]. |
| Embedded markdown templates | `to-prd`, `to-issues`, `triage`, `qa`, `request-refactor-plan` | Templates live inside `SKILL.md` bodies [F09] [F10] [F11] [F15] [F12]. |
| Separate reference/template files | `grill-with-docs`, `improve-codebase-architecture`, `triage`, `setup-matt-pocock-skills` | Supporting files are one level deep inside the skill folder [F23] [F24] [F25] [F26] [F22] [F30]. |
| HTML analysis artifacts | `improve-codebase-architecture`, `teach` | Architecture reports and teaching explainers are HTML artifacts [F08] [F18] [F25]. |
| Parallel subagents | `improve-codebase-architecture`, `design-an-interface`, `review`; deprecated `qa` uses background exploration | Used for codebase exploration or independent axes/design alternatives [F08] [F16] [F17] [F15]. |
| Durable handoff/artifact compaction | `handoff` | Writes a handoff document and references existing artifacts rather than duplicating content [F14]. |

The absence is as important as the presence: there is no general citation-backed research dossier template in the upstream repo. The closest analogues are the architecture HTML report, PRD template, issue/agent-brief templates, and writing/teaching artifacts [F08] [F09] [F10] [F18] [F22] [F25].

## Portability to the target skill/action/rule/template model

The target system is more structured than `mattpocock/skills`: a self-contained skill folder with `SKILL.md` frontmatter manifest, `actions/*.md`, immutable `rules/*.md`, `templates/*.md`, and a `rules.index.md` lazy index. The target's `AGENTS.md` emphasizes specialization, isolation, composability, lazy loading, action resolution, and a two-tier rule system where skill rules are immutable and project rules can override unless marked non-overridable.

### Minimalist prompt-skill vs structured action/rule/template skill

| Dimension | `mattpocock/skills` minimalist style | Target structured style | Research-skill implication |
|---|---|---|---|
| Unit of behavior | Mostly one `SKILL.md` prompt with embedded process [F05] [F07] [F09]. | Skill package with actions, rules, templates, and lazy-loading indexes. | Keep the manifest short, but move reusable research operations into actions. |
| Invocation | Trigger-heavy description; slash-command-like names [F21]. | Slash command or keyword match resolves to capabilities/actions. | Description should name "research", "investigate", "dossier", "citation-backed", "compare sources", and "design research" triggers. |
| Process | Imperative instructions, often all in one file. | Rules capture invariant process; actions capture task-specific workflows. | Put non-negotiables such as citation integrity and source verification in rules. |
| Templates | Often embedded in `SKILL.md`; supporting files one level deep when needed [F09] [F10] [F21]. | Dedicated `templates/*.md`. | Use a dossier template rather than embedding the whole format in `SKILL.md`. |
| Parallelism | Rare and explicit for independent axes or variants [F16] [F17]. | Can be formalized as an action rule. | Add a rule: parallelize only when research decomposes into independent source families or analytical axes. |
| Output location | Mixed: issue tracker, docs, OS temp, local markdown [F08] [F09] [F14] [F18]. | Project research/work artifacts likely live under project folders. | Prefer durable project paths for research dossiers; avoid temp for final research. |
| Source discipline | Some skills require reading docs/code; no universal citation protocol. | Can require citations in a skill rule. | Add citation IDs, source log, and "read files, do not guess" as immutable rules. |

### What to adopt

1. **Adopt trigger-first descriptions.** Copy the `write-a-skill` discipline: first sentence capability, second sentence "Use when..."; keep it under 1024 characters [F21].
2. **Adopt one-question-at-a-time grilling as a research action.** `grill-me` is a concise, proven pattern for eliciting unknowns and resolving decision trees [F05].
3. **Adopt code/doc cross-checking from `grill-with-docs`.** Research should challenge claims against repository context, domain language, and decision records [F06].
4. **Adopt feedback-loop thinking from `diagnose`.** For empirical claims, require a reproducer, benchmark, command output, or falsifiable hypothesis rather than prose confidence [F07].
5. **Adopt independent-axis parallelism from `review`.** Parallel subagents are most valuable when each has a separate lens and the aggregator preserves the separation [F17].
6. **Adopt visual/report artifact thinking from `improve-codebase-architecture`.** For design research, a structured artifact with findings, recommendations, and source-backed diagrams/tables is more useful than chat-only output [F08] [F25].
7. **Adopt durable, behavioral artifact guidance from `AGENT-BRIEF.md`.** Research outputs should avoid brittle file/line instructions unless exact citations are required; recommendations should be durable and testable [F22].
8. **Adopt glossary/ADR integration.** When research resolves terminology or uncovers a durable decision, route it into `CONTEXT.md`/learnings/decision records rather than leaving it buried in the dossier [F06] [F23] [F24].

### What to avoid

1. **Avoid a monolithic mega-prompt.** Matt Pocock's repo succeeds by being small, but a citation-backed research capability has enough invariant rules and templates to justify structure [F01] [F21].
2. **Avoid temp-only final outputs.** `improve-codebase-architecture` and `handoff` write to OS temp for their use cases [F08] [F14]. A research dossier should be durable and project-addressable.
3. **Avoid asking the user questions that sources can answer.** Both grill skills tell the agent to explore codebase instead when code can answer [F05] [F06].
4. **Avoid unbounded source gathering.** Borrow `diagnose`'s "build the right loop" discipline: define what evidence would answer the question, then stop once enough evidence is captured [F07].
5. **Avoid merging independent findings too early.** `review` intentionally preserves Standards and Spec as separate axes [F17]. A research skill should keep source-family findings separate until synthesis.
6. **Avoid user-specific paths.** `obsidian-vault` is useful as a personal pattern, but its hard-coded vault path is not portable [F31].

### Recommended target research-skill shape

```text
.agents/skills/research/
├── SKILL.md
├── actions/
│   ├── create-dossier.md
│   ├── compare-references.md
│   ├── investigate-codebase.md
│   ├── grill-research-plan.md
│   └── synthesize-to-plan.md
├── rules/
│   ├── rules.index.md
│   ├── citation-integrity.md
│   ├── source-verification.md
│   ├── parallel-research.md
│   ├── artifact-durability.md
│   └── claim-confidence.md
└── templates/
    ├── research-dossier.md
    ├── source-log.md
    └── recommendation-matrix.md
```

Suggested `SKILL.md` manifest:

```yaml
---
name: research
description: Produce citation-backed research, investigation, and analysis artifacts from code, docs, web, and repository sources. Use when the user asks to research, investigate, compare references, create a dossier, evaluate design options, or gather evidence before planning work.
---
```

Good: small manifest plus action/rule/template decomposition.

Bad: putting the entire dossier format, citation rules, web/code investigation process, and portability guidance into one long `SKILL.md`.

### Concrete action recommendations

| Target action | Upstream pattern to borrow | Target-specific addition |
|---|---|---|
| `create-dossier` | Architecture report structure from `improve-codebase-architecture`; PRD/issue template discipline from `to-prd`/`to-issues` [F08] [F09] [F10]. | Mandatory citation IDs, source log, claim confidence, and durable project output path. |
| `grill-research-plan` | `grill-me` and `grill-with-docs` one-question-at-a-time decision-tree interrogation [F05] [F06]. | Capture unresolved questions and sourceable claims in a research backlog. |
| `investigate-codebase` | `zoom-out`, `diagnose`, `triage` context gathering [F13] [F07] [F11]. | Standardize code search/read/verify loop and distinguish direct evidence from inference. |
| `compare-references` | `review` independent analysis axes and `design-an-interface` divergent subagent pattern [F17] [F16]. | Assign subagents by source family or analytical lens; aggregate without flattening disagreements. |
| `synthesize-to-plan` | `to-prd`, `to-issues`, `request-refactor-plan` [F09] [F10] [F12]. | Convert findings into target work packages, decisions, or future-enhancement ideas. |

## Sources

| ID | Source read |
|---|---|
| [F01] | `README.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/README.md` |
| [F02] | `CLAUDE.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/CLAUDE.md` |
| [F03] | `skills/engineering/README.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/README.md` |
| [F04] | `skills/productivity/README.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/productivity/README.md` |
| [F05] | `skills/productivity/grill-me/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/productivity/grill-me/SKILL.md` |
| [F06] | `skills/engineering/grill-with-docs/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/grill-with-docs/SKILL.md` |
| [F07] | `skills/engineering/diagnose/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/diagnose/SKILL.md` |
| [F08] | `skills/engineering/improve-codebase-architecture/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/improve-codebase-architecture/SKILL.md` |
| [F09] | `skills/engineering/to-prd/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/to-prd/SKILL.md` |
| [F10] | `skills/engineering/to-issues/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/to-issues/SKILL.md` |
| [F11] | `skills/engineering/triage/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/triage/SKILL.md` |
| [F12] | `skills/deprecated/request-refactor-plan/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/deprecated/request-refactor-plan/SKILL.md` |
| [F13] | `skills/engineering/zoom-out/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/zoom-out/SKILL.md` |
| [F14] | `skills/productivity/handoff/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/productivity/handoff/SKILL.md` |
| [F15] | `skills/deprecated/qa/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/deprecated/qa/SKILL.md` |
| [F16] | `skills/deprecated/design-an-interface/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/deprecated/design-an-interface/SKILL.md` |
| [F17] | `skills/in-progress/review/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/in-progress/review/SKILL.md` |
| [F18] | `skills/in-progress/teach/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/in-progress/teach/SKILL.md` |
| [F19] | `skills/engineering/prototype/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/prototype/SKILL.md` |
| [F20] | `skills/deprecated/ubiquitous-language/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/deprecated/ubiquitous-language/SKILL.md` |
| [F21] | `skills/productivity/write-a-skill/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/productivity/write-a-skill/SKILL.md` |
| [F22] | `skills/engineering/triage/AGENT-BRIEF.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/triage/AGENT-BRIEF.md` |
| [F23] | `skills/engineering/grill-with-docs/CONTEXT-FORMAT.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/grill-with-docs/CONTEXT-FORMAT.md` |
| [F24] | `skills/engineering/grill-with-docs/ADR-FORMAT.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/grill-with-docs/ADR-FORMAT.md` |
| [F25] | `skills/engineering/improve-codebase-architecture/HTML-REPORT.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/improve-codebase-architecture/HTML-REPORT.md` |
| [F26] | `skills/engineering/improve-codebase-architecture/LANGUAGE.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/improve-codebase-architecture/LANGUAGE.md` |
| [F27] | `skills/in-progress/writing-fragments/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/in-progress/writing-fragments/SKILL.md` |
| [F28] | `skills/in-progress/writing-shape/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/in-progress/writing-shape/SKILL.md` |
| [F29] | `skills/in-progress/writing-beats/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/in-progress/writing-beats/SKILL.md` |
| [F30] | `.claude-plugin/plugin.json` and `skills/engineering/setup-matt-pocock-skills/` listing, `https://github.com/mattpocock/skills/tree/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/engineering/setup-matt-pocock-skills` |
| [F31] | `skills/personal/obsidian-vault/SKILL.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills/personal/obsidian-vault/SKILL.md` |
| [F32] | Bucket listings: `skills/`, `skills/misc/README.md`, `skills/in-progress/README.md`, `skills/deprecated/README.md`, `skills/personal/README.md`, at `https://github.com/mattpocock/skills/tree/e3b90b5238f38cdea5996e16861dcae28ef52eda/skills` |
| [F33] | Git tree enumeration via `gh api repos/mattpocock/skills/git/trees/e3b90b5238f38cdea5996e16861dcae28ef52eda?recursive=1`, filtered for `SKILL.md` paths. |
| [F34] | `CONTEXT.md`, `https://github.com/mattpocock/skills/blob/e3b90b5238f38cdea5996e16861dcae28ef52eda/CONTEXT.md` |
