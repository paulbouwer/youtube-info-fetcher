# Update Issue

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_PROVIDER` and `$WORK_TRACKING_PROJECT` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Apply field-scoped or section-scoped non-destructive updates to an existing issue. Updates are idempotent — re-running with the same input produces no change.

## Flow

### Prerequisites

This action assumes all bundled rules have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Identify the target issue and the requested change.

Load project rules:

- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` — title format, status values, section heading strings, acceptance criteria format, Work Packages table format

Determine:

- Target issue (URL, number, or extracted from current branch name per git-workflow branch policy)
- Operation type — see the table below
- Operation payload (new value, criterion to add, criterion to tick, etc.)

### Step 2: Resolve Operation

**Goal:** Map the user's intent to one of the supported operations.

| Operation | Scope | Behaviour |
| --------- | ----- | --------- |
| Set title | Field | Replace title; validate against issue policy |
| Set type | Field | Change native issue type (Feature, Bug, Task) |
| Add label | Field | Append label if not already present (idempotent) |
| Remove label | Field | Remove label if present (idempotent) |
| Set status | Field | Update board Status field to one of the four standard values |
| Add acceptance criterion | Section | Append to Acceptance Criteria section with next `AC{nnn}` number |
| Edit acceptance criterion | Section | Replace the statement of an existing `AC{nnn}` row, preserving its tick state |
| Tick acceptance criterion | Section | Change `- [ ]` to `- [x]` for the targeted `AC{nnn}` |
| Untick acceptance criterion | Section | Change `- [x]` to `- [ ]` for the targeted `AC{nnn}` |
| Add Work Packages row | Section | Append a row idempotently (skip if Work Package column matches existing) |
| Append comment | Append-only | Post a new comment on the issue |

Closing an issue is OUT OF SCOPE for this action. Issues close via PR merge with `Fixes #N` per git-workflow rules.

### Step 3: Apply Section-Scoped Edits

**Goal:** For section operations, parse the issue body, locate the section by its heading anchor, mutate only that section, and write back.

Section heading anchors come from `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` § Section Headings. The parser MUST:

1. Read the current issue body via the provider CLI
2. Locate the target heading line by exact string match
3. Extract the slice between that heading and the next `###` heading (or end of body)
4. Mutate the slice per the operation
5. Reassemble the body, preserving every byte outside the targeted slice
6. Write the body back via the provider CLI

Verify idempotency: if the operation produces the same body content as the current body, skip the write and report no-op.

### Step 4: Apply Field Edits

**Goal:** For field operations (title, type, labels, status), use the provider CLI's native field-update commands.

For status updates, use the board's `Status` field on `$WORK_TRACKING_PROJECT`. Verify the new value is one of the standard status strings before issuing the update.

### Step 5: Confirm

**Goal:** Confirm the update was applied and report the resulting state.

Re-fetch the issue (or the affected field/section) and present a short summary of the change made.

## Error Handling

| Error | Recovery |
| ----- | -------- |
| Issue not found | Verify URL/number, check repository |
| Target section heading not found | Inform user, list detected headings, suggest the issue may need to be re-templated |
| Acceptance criterion ID not found | List existing AC IDs in the issue |
| Invalid status value | List the four standard values from project policy |
| Provider authentication failed | Inform user, reference dev-env rule |
| Update would be destructive (rejected by parser) | Refuse the operation, report which section content would have been lost |
