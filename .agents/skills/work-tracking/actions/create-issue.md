# Create Issue

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_PROVIDER` and `$WORK_TRACKING_PROJECT` from root `AGENTS.md`
- Inherits `$WORK_TRACKING_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Create a rules-compliant issue on the configured tracking provider, add it to the configured board, and set its initial status. The issue carries goal/problem, requirements/expectations, and acceptance criteria — never tasks (tasks live in the linked work package's `plan.md`).

## Flow

### Prerequisites

This action assumes all bundled rules have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Determine the issue type, title, and body content from available context.

Load project rules:

- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` — title format, type set, board status, section heading strings, acceptance criteria authoring
- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/templates/feature.md` — Feature body template
- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/templates/bug.md` — Bug body template
- `$AGENT_SYSTEM_FOLDER/rules/work-tracking/templates/task.md` — Task body template

Scan the conversation, repository state, and any draft work package for:

- The kind of work being tracked (Feature, Bug, or Task)
- A clear title statement
- Goal/Problem content
- Requirements/Expectations content
- Initial Acceptance Criteria

| Mode | When | Next Step |
|------|------|-----------|
| **Execute** | Type, title, and required body content are clear | Go to Step 2 |
| **Discover** | Information is missing — need to ask the user | Ask focused clarifying questions, then go to Step 2 |

If uncertain, default to **Discover**.

### Step 2: Construct and Validate

**Goal:** Build the issue title and body from the appropriate template, then validate against the issue policy.

Construct the title per `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` § Title Format. Validate length and case.

Construct the body by:

1. Selecting the template matching the issue type
2. Replacing placeholder commentary with authored content for Goal/Problem and Requirements/Expectations
3. Authoring the Acceptance Criteria list per the policy (`AC001`, `AC002`, ...)
4. Leaving the Work Packages table with its initial placeholder row — `create-work-package` will populate it later

Validate:

- Required sections present per `$WORK_TRACKING_SKILL/rules/issue.md` § Body Section Structure
- No Tasks section
- Acceptance criteria use `AC{nnn}` numbering and checkbox format
- Title within character limit

Present the complete issue preview (type, title, full body, target board, initial status) for user approval before creating.

### Step 3: Create and Place

**Goal:** Create the issue on the provider, add it to the configured board, and set initial status.

Create the issue using the appropriate provider CLI for `$WORK_TRACKING_PROVIDER`, passing the issue type natively (e.g. `gh issue create --type Feature ...` for GitHub). The body content is the assembled body from Step 2 — do NOT rely on the provider's template chooser; pass `--body` explicitly so the rule templates are the source of truth.

After creation:

1. Add the issue to the board named by `$WORK_TRACKING_PROJECT`
2. Set the `Status` field on that board to the project default (`Todo` per `$AGENT_SYSTEM_FOLDER/rules/work-tracking/issue.md` § Board Status)

Confirm creation with: issue URL, issue number, type, title, board, and initial status.

## Error Handling

| Error | Recovery |
| ----- | -------- |
| Missing required body content | Re-enter Discover mode and ask for the missing section content |
| Title exceeds limit | Show the limit, offer a trimmed title for confirmation |
| Provider authentication failed | Inform user, reference `$AGENT_SYSTEM_FOLDER/rules/work-tracking/devcontainer.md` for setup |
| Board not found | List available boards, ask for selection or correction of `$WORK_TRACKING_PROJECT` |
| Status field or value not found on board | Inform user, list available status values, suggest updating the project board configuration |
| Issue creation failed | Display provider error, check permissions |
