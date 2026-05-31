# Create Work Package

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_PROVIDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Scaffold a rules-compliant work package in `$PROJECT_FOLDER/work/`. If an issue exists, set the `related:` frontmatter and append a back-link row to the issue's Work Packages table. If no issue exists, create a draft work package — the issue link is enforced later, at the pull-request boundary.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Determine the work package short description, today's date, and any existing issue to link.

Load project rules:

- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` — frontmatter, draft phase, file section conventions
- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` — Work Packages table format (for back-link)

Scan the conversation, repository state, and any draft planning artefacts for:

- A 3-7 word short description for the folder name
- An associated issue URL (if one exists)
- Initial content for `spec.md` (problem, requirements, success criteria)
- Initial content for `plan.md` (approach, phases)

| Mode | When | Next Step |
|------|------|-----------|
| **Execute** | Description and initial content are clear | Go to Step 2     |
| **Discover** | Missing information — ask focused questions | Then go to Step 2 |

### Step 2: Construct Folder Name and Validate

**Goal:** Build the folder name per the naming convention and verify it does not already exist.

Construct the folder name per `$WORK_TRACKING_SKILL/rules/work-package.md` § Naming Convention:

```text
$PROJECT_FOLDER/work/YYYY-MM-DD-{short-description}/
```

Validate:

- Date prefix is today's ISO date
- Description is 3-7 words, lowercase, hyphen-separated, alphanumeric
- No folder of the same name exists

If the folder already exists, inform the user and offer to choose a different description or use `update-work-package` on the existing folder.

### Step 3: Scaffold Files

**Goal:** Create `spec.md` and `plan.md` with required sections and the appropriate frontmatter state.

Create `spec.md` with the section headings defined in `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` § spec.md Sections. Populate sections with authored content from Step 1; leave any section the user did not provide as a stub with a comment explaining what to fill in.

Create `plan.md` with frontmatter and the section headings defined in `$AGENT_SYSTEM_FOLDER/rules/work-tracking/work-package.md` § plan.md Sections.

Frontmatter rules:

| Issue State | Frontmatter |
| ----------- | ----------- |
| Issue URL provided | `related: {issue-url}` |
| No issue yet | Frontmatter MAY be omitted entirely, OR include `related: TBD` to make the draft state explicit |

Populate Phases with task checkboxes derived from the user's intent; if no detail is available, leave a single placeholder phase as a stub.

### Step 4: Apply Back-Link (if linked)

**Goal:** If an issue was linked, append a row to the issue's Work Packages table.

If `related:` was set in Step 3:

1. Fetch the issue body via the provider CLI
2. Locate the `### 📋 Work Packages` heading per `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` § Section Headings
3. Parse the existing table; if a row whose Work Package column matches the new work package folder name is already present, skip — back-link is idempotent
4. Otherwise, append a row in the format defined by `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` § Work Packages Table Format:

   ```markdown
   | {folder-name} | {one-line description} |
   ```

5. Write the updated body back via the provider CLI, preserving all unrelated content

If no issue was linked, skip this step. Inform the user that the work package is in *draft* state and that `related:` MUST be set via `update-work-package` before pull-request creation.

### Step 5: Confirm

**Goal:** Report the created work package and its linkage state.

Confirm with: folder path, files created, frontmatter state (linked vs draft), issue URL (if applicable), and back-link result.

## Error Handling

| Error | Recovery |
| ----- | -------- |
| Folder already exists | Offer to use `update-work-package` or choose a different description |
| Description fails validation | Show validation rule, ask for correction |
| Issue URL malformed | Verify URL format, ask for correction |
| Issue not found at URL | Inform user, offer to proceed as draft |
| Back-link write failed | Work package is created; report partial success and suggest re-running `update-work-package` to retry the back-link |
