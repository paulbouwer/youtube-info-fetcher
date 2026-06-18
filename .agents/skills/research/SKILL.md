---
name: research
description: Produce citation-backed research and synthesis artifacts from code, docs, and web sources, standalone or inside a work package. Use when the user wants to research, investigate, create a dossier, compare references, gather evidence, or synthesise findings before planning work.
---

# Research Skill

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$RESEARCH_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

This skill provides capabilities for producing durable, citation-backed research artifacts and synthesising them into a position, from code, docs, and web sources, standalone or inside a work package.

## Configuration

### Capabilities

| Capability | Action | Description | Load when |
|------------|--------|-------------|-----------|
| Create Research | `$RESEARCH_SKILL/actions/create-research.md` | Resolve the output location, run research (mode `single` by default, or `focused-swarm` across user-described lenses), and write one cited dossier from the template, stopping at the read-only boundary | User wants to research or investigate a topic and capture a dossier |
| Synthesise Research | `$RESEARCH_SKILL/actions/synthesise-research.md` | Consume one or more dossiers and produce a summary and position document with an opinion and a Gaps / Next Research section | User wants to consolidate one or more dossiers into a position |

### Rules

| Index | File |
|-------|------|
| Skill Rules Index | `$RESEARCH_SKILL/rules/rules.index.md` |

Consult each index and load only the rule files whose **Load when** matches the selected capability.

### Bundled Templates

| Template | File | Load when |
|----------|------|-----------|
| Research Dossier | `$RESEARCH_SKILL/templates/research-dossier.md` | Running the Create Research capability (writing a dossier) |
| Summary and Position | `$RESEARCH_SKILL/templates/summary-position.md` | Running the Synthesise Research capability (writing a position) |

## Flow

### Prerequisites

- [ ] Verify `$RESEARCH_SKILL/rules/` is accessible
- [ ] Verify `$RESEARCH_SKILL/templates/` is accessible

### Execution Steps

1. Determine the user's intent and select the matching capability from the **Capabilities** section using its **Load when** trigger
2. Load skill rules via the **Rules** index — only the rules whose **Load when** matches the selected capability
3. Load bundled templates whose **Load when** matches the selected capability
4. Execute the selected capability
