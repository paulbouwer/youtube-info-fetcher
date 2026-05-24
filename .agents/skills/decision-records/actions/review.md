# Review Decision Record

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$DECISION_RECORDS_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Analyse an existing decision record for completeness, quality, and rules compliance, then provide actionable recommendations.

## Flow

### Prerequisites

This action assumes all bundled rules and templates have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Locate Decision Record

**Goal:** Identify the decision record to review.

| Method | Action |
|--------|--------|
| User-specified | Use the file path provided by the user |
| Search | Look in `$PROJECT_FOLDER/decision-records/` |
| Recent | List recent decision records for the user to select |

If no decision record is found, recommend the Create action.

### Step 2: Analyse

**Goal:** Evaluate the decision record for structural compliance and content quality.

#### Structural Validation

Validate the decision record against `$DECISION_RECORDS_SKILL/rules/checklist.md`. The checklist covers structure, required sections, Y-Statement completeness, naming, location, status, and actionability.

#### Content Quality

Assess the substance beyond structural compliance:

| Factor | Questions |
|--------|-----------|
| **Context Depth** | Does it explain why this decision was needed? Are constraints documented? |
| **Alternatives Quality** | Are alternatives genuinely different? Are pros/cons balanced? Is rejection reasoning clear? |
| **Consequences Honesty** | Are risks acknowledged? Are benefits realistic? |
| **Actionability** | Could someone implement based on this record? |

### Step 3: Report

**Goal:** Present findings as a structured review with a score, issues grouped by severity, and specific recommendations.

#### Scoring Rubric

Use the following categories and their relative importance to arrive at an overall score out of 10:

| Category | Weight | Guidance |
|----------|--------|----------|
| Required Sections | High | Are all 7 sections present and substantive? |
| Y-Statement | High | Are all 5 clauses present, specific, and coherent? |
| Alternatives | Medium | At least one with genuine trade-off analysis? |
| Consequences | Medium | Both benefits and risks acknowledged honestly? |
| Naming and Metadata | Low | Filename, status, date, and deciders correct? |

| Score | Rating | Recommendation |
|-------|--------|----------------|
| 9-10 | Excellent | Ready for acceptance |
| 7-8 | Good | Minor improvements recommended |
| 5-6 | Adequate | Address gaps before accepting |
| 3-4 | Needs Work | Significant revisions required |
| 0-2 | Incomplete | Return to Create flow |

#### Report Structure

Organise findings into these sections:

1. **Score** — Overall completeness score (X/10) with rating
2. **Critical** — Missing required elements that must be fixed (missing Y-Statement, no alternatives, no consequences)
3. **Compliance** — Rules violations with specific fixes (naming format, missing metadata, incomplete sections)
4. **Improvements** — Suggestions to strengthen the record (vague context, weak rationale, unclear trade-offs)
5. **Commendations** — What the record does well
