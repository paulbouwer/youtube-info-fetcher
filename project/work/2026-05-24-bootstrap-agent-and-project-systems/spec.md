## Problem

This repository was created empty. Before any feature work can begin, the agent system and project system must be in place so contributors and AI agents share a common vocabulary, set of skills, and process. Without this scaffolding, work cannot be planned, tracked, or executed in a way that complies with the documented git-workflow and work-tracking processes.

## Requirements

- Root `AGENTS.md` defines the project identity (description, agent-system variables) and references both pillars.
- Agent system at `.agents/` provides:
  - **Skills:** agent-system, decision-records, devcontainer, git-workflow, grill-me, work-tracking — each with its `SKILL.md`, actions, and skill rules.
  - **Project rules tree** at `.agents/rules/` covering git-workflow, work-tracking, formats, languages (Go, bash), tools (gh, git, just), and devcontainer/vscode/development-environment.
  - Lazy-loading indices (`rules.index.md`) at root and per category.
- Project system at `project/` provides folders for decision records and work packages, plus root documents `future-enhancement-ideas.md` and `learnings.md`.
- Configured project constraints (captured in the rules tree):
  - **Language:** Go (cross-platform CLI)
  - **Shell tooling:** bash
  - **Task runner:** just
  - **Repository provider:** GitHub
  - **Work tracking provider:** GitHub (Projects v2 board: `YouTube Info Fetcher`)

## Out of Scope

- DevContainer image build and tooling installation (covered by a future Task issue).
- Initial Go module structure and CLI scaffolding (covered by a future Feature issue).
- CI/CD pipeline (covered by a future Task issue).

## Success Criteria

- All six acceptance criteria on issue #1 pass review.
- A subsequent issue/branch/commit/PR on this repository can be created end-to-end using the documented skills without rule violations.
- The labels `Feature`, `Bug`, `Task` exist and map to the issue-type vocabulary.
