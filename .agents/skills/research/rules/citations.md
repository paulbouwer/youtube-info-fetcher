# Research Citations Rule

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$RESEARCH_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Quick Reference

Citation integrity for research artifacts. Every claim is traceable to a labelled source; sources are read, not guessed.

| Item | Rule |
|------|------|
| **Source index** | A mandatory `[F##]` source index; each entry referenced inline at the point of use |
| **Local references** | Plain workspace paths with line ranges — no `#file:` directives |
| **External references** | Markdown links to the URL |
| **Read, do not guess** | Open and read each source before citing it |
| **Evidence vs inference** | Direct evidence is labelled and kept separate from the author's inference |

## Source Index

Every research artifact **MUST** carry a source index — a numbered list of every source the artifact draws on, each with a stable `[F##]` identifier (`[F01]`, `[F02]`, ...).

- Each `[F##]` entry **MUST** be referenced inline at the point where its evidence is used, so a reader can trace any claim to its source.
- An entry that is never referenced inline **MUST NOT** appear in the index — the index records sources actually used, not a reading list.
- A claim that carries no `[F##]` reference is treated as unsupported and **MUST** be moved to inference (see Evidence vs Inference) or removed.

## Reference Formats

The reference format depends on whether the source is local to the workspace or external.

| Source | Format | Example |
|--------|--------|---------|
| Local workspace file | Plain path with a line range | `$PROJECT_FOLDER/work/2026-04-05-cli/spec.md:18-24` |
| External resource | Markdown link to the URL | `[Cobra documentation](https://example.com/cobra)` |

- Local references **MUST** be plain paths. Do **not** use `#file:` directives or other editor-specific link syntax.
- Local references **SHOULD** include a line range when citing a specific passage, so the evidence is locatable.
- External references **MUST** be markdown links so the URL is both readable and navigable.

Good: A claim followed by its source — the parser rejects empty input [F03], where `[F03]` resolves to `internal/parse/parse.go:42-50` in the source index.

Bad: A claim citing an editor directive — the parser rejects empty input (`#file:parse.go`) — using `#file:` syntax and no line range.

## Read Sources, Do Not Guess

Every source **MUST** be opened and read before it is cited. Citations stand for evidence the author has actually seen.

- **MUST NOT** cite a file, line range, or URL from memory or assumption.
- **MUST NOT** infer a source's contents from its name, path, or surrounding context.
- When a source cannot be read (access denied, missing, unreachable), record the gap in the dossier's Gaps / Next Research section rather than citing it speculatively.

## Evidence vs Inference

Direct evidence and the author's inference are different things and **MUST** be kept visibly separate.

- **Direct evidence** is what a source states or demonstrates: a passage in a file, command or test output, a benchmark result, or reproduction steps. Each piece of evidence is labelled and carries its `[F##]` reference.
- **Inference** is the author's reasoning built on that evidence. Inference is presented as such — not dressed up as a cited fact.

Keeping the two separated lets a reader audit which conclusions rest on observed evidence and which rest on judgement.
