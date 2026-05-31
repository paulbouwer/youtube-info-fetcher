# Create Decision Record

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Guide the creation of a decision record — either by extracting a decision from an existing conversation or by facilitating structured discovery when starting fresh.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Assess Context

**Goal:** Determine what information is already available before deciding how to proceed.

**Scan the conversation and any referenced materials for:**

- The decision or problem being discussed
- Options that were considered
- Trade-offs or constraints mentioned
- The chosen approach and reasoning

**Select the appropriate mode:**

| Mode | When | Next Step |
|------|------|-----------|
| **Document** | The decision has already been discussed — context contains the problem, options, and rationale | Go to Step 3 |
| **Discover** | The decision is new or unclear — context lacks the information needed to construct a complete record | Go to Step 2 |

If uncertain, default to **Discover** — it is better to confirm understanding than to assume.

### Step 2: Guided Discovery

**Goal:** Build shared understanding of the decision through conversation.

Only execute this step in **Discover** mode. In **Document** mode, skip to Step 3.

**Understand the problem.** Start with one of these based on what is missing:

- "What decision are you facing?"
- "What problem or situation is driving this decision?"
- "What will be different after this decision is made?"

Then explore the dimensions needed to construct a complete record:

| Dimension | Question | Adapt For |
|-----------|----------|-----------|
| **Stakeholders** | "Who else is affected by this decision?" | |
| **Constraints** | "What constraints are you working within?" | Technology: stack fit, vendor lock-in |
| **Alternatives** | "What options have you considered?" | Architecture: scalability, complexity |
| **Trade-offs** | "What are the pros and cons of each option?" | Process: adoption, tooling, transition |
| **Risks** | "What could go wrong with the chosen approach?" | Security: threat model, compliance |
| **Success** | "How will you know this was the right decision?" | |

Adapt the depth and focus based on the decision type (right column) and the user's experience level. For experienced users, focus on challenging assumptions and exploring edge cases. For those newer to the domain, provide more context and connect choices to outcomes.

**Never fabricate alternatives.** Only document options the user actually considered. If the user says they considered no alternatives, document that with the reasoning.

When helpful, use search tools to find external documentation, implementation patterns, or existing codebase conventions that inform the decision.

Continue the conversation until the following are clear:

- The core decision in one sentence
- At least one alternative that was considered (with trade-offs)
- The chosen approach and why

### Step 3: Construct Y-Statement

**Goal:** Produce the Y-Statement that anchors the decision record.

The Y-Statement is required per `$DECISION_RECORDS_SKILL/rules/core.md`:

```
In the context of {situation/problem},
facing {specific concern or constraint},
we decided {what was chosen},
to achieve {desired outcome},
accepting {trade-off or downside}.
```

**In Document mode:** Draft the Y-Statement from existing context and present it for confirmation. The user should only need to approve or adjust — not re-explain the decision.

**In Discover mode:** Work through each clause with the user, building the statement collaboratively.

**Example:**

> In the context of needing consistent API authentication,
> facing multiple client types with different security requirements,
> we decided to implement OAuth 2.0 with JWT tokens,
> to achieve standardised, stateless authentication across all clients,
> accepting the additional complexity of token management and refresh flows.

If any clause is unclear, work through it individually. The Y-Statement must be confirmed by the user before proceeding.

### Step 4: Generate and Confirm

**Goal:** Produce the complete decision record and get user approval before writing.

**Validate against `$DECISION_RECORDS_SKILL/rules/checklist.md`** — confirm all required elements are present before generating the document.

**Generate the decision record** using `$DECISION_RECORDS_SKILL/templates/decision-record.md`:

1. **Filename**: `{YYYY-MM-DD}-{short-description}.md` per `$DECISION_RECORDS_SKILL/rules/core.md`
2. **Location**: `$PROJECT_FOLDER/decision-records/`
3. **Status**: `Proposed` unless user specifies otherwise

Present the complete decision record for approval. Do not create the file until the user explicitly approves.

### Step 5: Create and Report

**Goal:** Write the approved file.

1. Create `$PROJECT_FOLDER/decision-records/` if it does not exist
2. Write the approved content — if filename conflicts, construct a new, unique and appropriate {short-description} for the filename pattern

Confirm creation and summarise next steps:

- Review and update status to `Accepted` after approval
- Submit via Pull Request for team review

## Error Handling

| Scenario | Action |
|----------|--------|
| User unsure of decision | Return to Step 2 (Guided Discovery) |
| No alternatives provided | Ask: "What other options did you consider?" — require at least one |
| User says "none" to alternatives | Document as "No formal alternatives evaluated" with the reasoning |
| Unclear consequences | Ask: "What happens if this goes wrong?" |
| Y-Statement unclear | Work through each clause individually |
| Filename conflict | Construct a new, unique and appropriate {short-description} for the filename |
| No `$PROJECT_FOLDER/decision-records/` directory | Create it |
