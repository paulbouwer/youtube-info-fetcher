# Decision Record: Establish repository purpose and audience

- **Status:** Accepted
- **Deciders:** Paul Bouwer
- **Date:** 24 May 2026
- **Related Docs:** [Issue #1](https://github.com/paulbouwer/youtube-info-fetcher/issues/1)

## Summary (Y-Statement)

_**In the context of**_ creating a new repository for fetching YouTube video metadata,
_**facing**_ the need to set a clear scope and identify the primary consumer before tooling and rules are chosen,
_**we decided**_ to position this repository as a cross-platform command-line tool whose primary consumer is AI agents (with humans as a secondary consumer),
_**to achieve**_ a single, focused, scriptable interface for retrieving YouTube titles, descriptions, transcripts, and related metadata,
_**accepting**_ that AI-agent-first design constraints (deterministic output, structured exit codes, machine-readable formats) take precedence over interactive human ergonomics when they conflict.

## Context

The repository is empty and needs a clear purpose before scaffolding decisions can be made. Without a defined audience and scope, technology and process choices risk being incoherent with the eventual use case.

Multiple consumer profiles were considered: human users at a terminal, scripts in CI/CD pipelines, and AI agents invoking the tool as a sub-process. The latter is the primary motivation for this project — current AI-agent workflows need a reliable way to retrieve YouTube video metadata without each agent reimplementing API integration.

## Decision

This repository delivers a single CLI binary, `youtube-info-fetcher`, with the following positioning:

- **Primary consumer:** AI agents invoking the binary as a sub-process.
- **Secondary consumer:** humans and scripts that benefit from a focused metadata-retrieval CLI.
- **Scope:** YouTube video titles, descriptions, transcripts, and other publicly available metadata.
- **Distribution:** cross-platform binary (Linux, macOS, Windows).

Design priorities follow from the primary consumer: deterministic output, structured exit codes, machine-readable formats (JSON-first), and unambiguous error reporting.

## Consequences

### Benefits

- A clear north star for all subsequent scaffolding, technology, and process decisions.
- AI agents can consume the tool consistently without bespoke wrapping.
- Simpler API surface than a multi-purpose YouTube toolkit.

### Risks & Trade-offs

- Human-interactive ergonomics (colour, progress bars, prompts) are intentionally deprioritised.
- Feature requests that benefit only human consumers may be declined or deferred.
- Cross-platform parity adds testing and packaging overhead.

## Alternatives Considered

- This repository is intentionally a focused learning vehicle for AI-agent-consumable CLI patterns; broader alternatives such as a general-purpose YouTube SDK or a human-first interactive CLI were not formally explored.

## Notes

Subsequent technology choices (Go, GitHub, bash, just, MIT) are recorded separately in `2026-05-24-adopt-foundational-tech-stack.md`, which references this decision as its motivating context.
