# Decision Record: Adopt foundational tech stack

- **Status:** Accepted
- **Deciders:** Paul Bouwer
- **Date:** 24 May 2026
- **Related Docs:** [Repository purpose DR](./2026-05-24-establish-repository-purpose-and-audience.md), [Issue #1](https://github.com/paulbouwer/youtube-info-fetcher/issues/1)

## Summary (Y-Statement)

_**In the context of**_ scaffolding a cross-platform CLI whose primary consumer is AI agents,
_**facing**_ the need to choose an implementation language, tooling, and provider set that the agent system rules can encode and enforce from day one,
_**we decided**_ to adopt Go as the implementation language, bash and just as the local tooling baseline, GitHub for repository hosting and work tracking, and the MIT license,
_**to achieve**_ a single static binary per platform, a uniform local developer experience, an issue-driven workflow that AI agents can navigate, and unrestricted reuse,
_**accepting**_ Go's specific idioms and tooling, bash's POSIX assumptions on the developer machine, and GitHub vendor lock-in for issues and projects.

## Context

The repository purpose decision (see Related Docs) defined a cross-platform, AI-agent-first CLI as the deliverable. The agent system and rules tree at `.agents/` encode language, tooling, and provider assumptions in their `languages/`, `tools/`, `git-workflow/`, and `work-tracking/` categories. These assumptions must be aligned and recorded so that:

- Project rules under `.agents/rules/` (languages, tools, git-workflow, work-tracking, devcontainer) are internally coherent.
- Future contributors and AI agents understand the constraints baked into the rules.
- The choices are reviewable and reversible via subsequent decision records.

## Decision

| Concern | Choice | Rationale |
| ------- | ------ | --------- |
| Implementation language | **Go** | Static cross-platform binaries, strong standard library, predictable performance, mature CLI ecosystem (cobra/urfave), and excellent fit for AI-agent sub-process consumption. |
| Shell tooling baseline | **bash** | Universally available on Linux/macOS and on Windows via WSL or Git Bash; aligns with developer-machine assumptions in the devcontainer rule. |
| Task runner | **just** | Cross-platform recipe runner with a simple, file-based interface; replaces Makefile in a way that's friendlier to AI-agent invocation. |
| Repository provider | **GitHub** | Aligns with `$REPO_PROVIDER` in root `AGENTS.md`; chosen for ubiquity and integration with the work-tracking and CI tooling adopted next. |
| Work tracking provider | **GitHub** (Projects v2) | Aligns with `$WORK_TRACKING_PROVIDER`; co-locates issues, projects, and PRs on a single platform with native automation. |
| License | **MIT** | Permissive, unambiguous, and removes friction for AI-agent and human reuse alike. |

## Consequences

### Benefits

- A single static binary per supported OS is straightforward to ship and to invoke from any agent runtime.
- The rules tree at `.agents/rules/{languages,tools,git-workflow,work-tracking}` can be authored with concrete, non-speculative content.
- Work tracking, code review, and CI all live on one provider, simplifying the lifecycle.
- MIT removes legal friction for adoption and reuse.

### Risks & Trade-offs

- Vendor coupling to GitHub for issues and projects; migration would be non-trivial.
- Go's tooling choices (modules, golangci-lint, etc.) become baked into rules and CI.
- bash assumptions complicate development on Windows hosts without WSL or Git Bash.
- just is less ubiquitous than make; contributors may need a one-time install.

## Alternatives Considered

- Alternatives were not formally evaluated. Go is well understood by the deciders, produces single static binaries cross-platform with minimal ceremony, and fits the AI-agent sub-process invocation model directly.

## Notes

Subsequent technology adoptions (e.g. specific Go CLI framework, transcript-fetching library, CI provider configuration) will be recorded as their own decision records and reference this DR.
