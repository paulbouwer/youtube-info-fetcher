# YouTube Info Fetcher

## Variables

- `AGENT_SYSTEM_FOLDER := .agents`
- `PROJECT_FOLDER := project`
- `REPO_PROVIDER := github`
- `WORK_TRACKING_PROVIDER := github`
- `WORK_TRACKING_PROJECT := YouTube Info Fetcher`

## Project

A cross-platform CLI for fetching YouTube video titles, descriptions, transcripts, and other metadata. Built in Go and designed for consumption by AI agents.

## Operations

### Agent System

This repository supports AI-assisted engineering. **CONSULT** `$AGENT_SYSTEM_FOLDER/AGENTS.md` for available skills and rules.

### Project System

Project documentation and planning artefacts for the repo are located in the `$PROJECT_FOLDER` folder.

```text
$PROJECT_FOLDER/
├── decision-records/             # Architectural and technical decisions
├── work/                         # Work packages (planned units of work)
├── research/                     # Standalone citation-backed research artifacts
├── future-enhancement-ideas.md   # Ideas beyond current scope 
└── learnings.md                  # Cross-cutting patterns and insights
```

- **Decision Records** - Capture the context, rationale, and consequences of significant decisions related to the repo. Each decision record is located in the `$PROJECT_FOLDER/decision-records/` folder. **USE** the Decision Record skill and rules when working with decision records

- **Work Packages** - Primary unit of planned work. Each work package is a folder in `$PROJECT_FOLDER/work/` containing a specification, plan, research, and verification results. Work packages are linked with a remote issue on the configured work tracking provider. **USE** the Work Tracking skill and rules when working with work packages.

- **Research** - Citation-backed research artifacts (dossiers and position syntheses). Standalone research is located in `$PROJECT_FOLDER/research/<dated-slug>/`; research tied to a work package lives in that work package's `research/` folder. **USE** the Research skill and rules when producing or synthesising research.

- **Future Enhancement Ideas** - Enhancement ideas and feature concepts are captured in `$PROJECT_FOLDER/future-enhancement-ideas.md`. **CONSULT** when planning new capabilities or looking for contribution ideas. **UPDATE** when a planning session surfaces ideas beyond current scope.

- **Learnings** - Cross-cutting patterns and insights are documented in `$PROJECT_FOLDER/learnings.md`. **CONSULT** before making architectural decisions or adding new patterns. **UPDATE** after completing significant work that reveals reusable insights.

### Git Workflow

This repository uses `$REPO_PROVIDER` as its repository provider. 

- **Branch, Commit, PR** - Branch names must be issue-linked, commits must follow conventional commit format with AI attribution, commits must be signed, and pull requests must link to the originating issue. **USE** the Git Workflow skill and rules when performing git operations (branching, commits, pull requests).

### Work Tracking

This repository uses `$WORK_TRACKING_PROVIDER` as its work tracking provider, with issues organised on the `$WORK_TRACKING_PROJECT` board. 

- **Issues, Work Packages, Boards** - Every issue is created with a goal/problem, requirements/expectations, and acceptance criteria; every work package links to its issue via `related:` frontmatter; every issue is added to the configured board with a `Todo` status on creation. **USE** the Work Tracking skill and rules for all work - both local (work packages) and remote (issues).