# Decision Records Index

Quick reference for all decision records in this project.

## How to Use This Index

This index provides a summary of all decision records with their status and Y-Statement for quick scanning. For full details, read the linked decision record file.

**Maintained by:** The **decision-records** skill updates this index when decision records are created, updated, or deprecated.

---

## Active Records

| File | Status | Y-Statement |
| ---- | ------ | ----------- |
| [2026-05-24-establish-repository-purpose-and-audience.md](2026-05-24-establish-repository-purpose-and-audience.md) | Accepted | In the context of creating a new repository for fetching YouTube video metadata, facing the need to set a clear scope and identify the primary consumer before tooling and rules are chosen, we decided to position this repository as a cross-platform command-line tool whose primary consumer is AI agents (with humans as a secondary consumer), to achieve a single, focused, scriptable interface for retrieving YouTube titles, descriptions, transcripts, and related metadata, accepting that AI-agent-first design constraints (deterministic output, structured exit codes, machine-readable formats) take precedence over interactive human ergonomics when they conflict. |
| [2026-05-24-adopt-foundational-tech-stack.md](2026-05-24-adopt-foundational-tech-stack.md) | Accepted | In the context of scaffolding a cross-platform CLI whose primary consumer is AI agents, facing the need to choose an implementation language, tooling, and provider set that the agent system rules can encode and enforce from day one, we decided to adopt Go as the implementation language, bash and just as the local tooling baseline, GitHub for repository hosting and work tracking, and the MIT license, to achieve a single static binary per platform, a uniform local developer experience, an issue-driven workflow that AI agents can navigate, and unrestricted reuse, accepting Go's specific idioms and tooling, bash's POSIX assumptions on the developer machine, and GitHub vendor lock-in for issues and projects. |

<!-- Template for entries:
| [YYYY-MM-DD-short-description.md](YYYY-MM-DD-short-description.md) | Proposed/Accepted | In the context of X, facing Y, we decided Z, to achieve A, accepting B. |
-->

---

## Deprecated Records

Records that are no longer recommended. Moved to `deprecated/` folder.

| File | Status | Y-Statement | Superseded By |
| ---- | ------ | ----------- | ------------- |
