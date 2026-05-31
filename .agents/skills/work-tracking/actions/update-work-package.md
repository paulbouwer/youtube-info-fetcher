# Update Work Package

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_PROVIDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Apply field-scoped or section-scoped non-destructive updates to an existing work package. Updates are idempotent and preserve all unrelated content. Setting `related:` for the first time also applies the back-link to the issue's Work Packages table.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Identify the target work package and the requested change.

Load project rules:

- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` — frontmatter, draft phase rules, file section conventions
- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` — Work Packages table format (for back-link operations)

Determine:

- Target work package folder (path under `$PROJECT_FOLDER/work/`, or extracted from current branch context)
- Operation type — see the table below
- Operation payload

### Step 2: Resolve Operation

**Goal:** Map the user's intent to one of the supported operations.

| Operation | Target | Scope | Behaviour |
| --------- | ------ | ----- | --------- |
| Set / change `related:` | `plan.md` frontmatter | Field | Replace `related:` value; if previously set to a different issue, also remove the back-link row from the previous issue's Work Packages table and add it to the new one |
| Edit spec.md section | `spec.md` | Section | Replace section content for one of the standard headings; preserve all other sections |
| Edit plan.md section | `plan.md` | Section | Replace section content for one of the standard headings; preserve frontmatter and other sections |
| Tick plan task | `plan.md` Phases section | Section | Change `- [ ]` to `- [x]` for the targeted task line |
| Untick plan task | `plan.md` Phases section | Section | Change `- [x]` to `- [ ]` for the targeted task line |
| Add research file | `research/` | Append-only | Create a new file under `research/` named `YYYY-MM-DD-{topic}.md` with the supplied content |
| Append to verification-results | `verification-results.md` | Append-only | Create file if it doesn't exist; append a new entry without modifying existing entries |

### Step 3: Apply Section-Scoped Edits

**Goal:** For section operations, parse the target file, locate the section by its heading anchor, mutate only that section, and write back.

Section anchors come from `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` § File Section Conventions. The parser MUST:

1. Read the current file
2. Locate the target heading by exact string match
3. Extract the slice between that heading and the next heading at the same level (or end of file)
4. Mutate the slice per the operation
5. Reassemble the file, preserving frontmatter, every byte outside the targeted slice, and final newline

Verify idempotency: if the operation produces the same file content as the current file, skip the write and report no-op.

### Step 4: Apply Frontmatter / related: Updates

**Goal:** For `related:` updates, modify YAML frontmatter and synchronise the back-link on the issue side.

Frontmatter mutation rules:

1. If `plan.md` has no frontmatter, prepend one with the new `related:` value
2. If `plan.md` has frontmatter without `related:`, insert the key
3. If `plan.md` has `related:` with a different value, replace the value

Back-link synchronisation (per `$WORK_TRACKING_SKILL/rules/core.md` § Bidirectional Linkage):

1. If the previous `related:` value pointed to a real issue, fetch that issue's Work Packages table and remove the row whose Work Package column matches this work package folder
2. If the new `related:` value points to a real issue, fetch that issue's Work Packages table and append a row whose Work Package column matches this work package folder (idempotent — skip if already present)

Both back-link mutations follow the section-scoped non-destructive edit pattern described in `$WORK_TRACKING_SKILL/actions/update-issue.md` Step 3.

### Step 5: Append-Only Operations

**Goal:** For research files and verification-results entries, create or append without touching existing content.

Research files are always created fresh — never overwrite an existing research file. If a file with the same name exists, refuse the operation and ask for a different topic.

Verification-results appends add a new dated section to the bottom of the file, preserving all existing sections.

### Step 6: Confirm

**Goal:** Report the change made and any side-effects.

Confirm with: file path, operation, idempotency outcome (changed vs no-op), and any issue-side back-link mutation that occurred.

## Error Handling

| Error | Recovery |
| ----- | -------- |
| Work package folder not found | List existing work packages under `$PROJECT_FOLDER/work/`, ask for correction |
| Target section heading not found | Inform user, list detected headings, suggest the file may need re-scaffolding |
| Frontmatter is malformed YAML | Refuse the update, report the parse error |
| Issue not reachable for back-link sync | Apply the local change, report the back-link sync as deferred and suggest re-running once the issue is reachable |
| Update would be destructive (rejected by parser) | Refuse the operation, report which content would have been lost |
| Research file already exists | Refuse, ask for a different topic name |
