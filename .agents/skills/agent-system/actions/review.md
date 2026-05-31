# Review Agent-System Asset

## Variables

- Inherits `$AGENT_SYSTEM_FOLDER` and `$PROJECT_FOLDER` from root `AGENTS.md`
- Inherits `$AGENT_SYSTEM_SKILL` from `$AGENT_SYSTEM_FOLDER/AGENTS.md`

## Purpose

Validate one or more agent-system assets against the bundled validation checklist, identify the asset type per file, run the matching checklist groups, and report findings grouped by severity. Read-only — does not modify files.

## Flow

### Prerequisites

This action assumes the rules relevant to this capability — and any templates, where the skill bundles them — have been loaded into context by `SKILL.md` before execution begins.

### Step 1: Locate Targets

**Goal:** Identify the file(s) to review.

| Method | Action |
|--------|--------|
| User-specified path(s) | Use the path(s) provided |
| Skill folder | Walk `$AGENT_SYSTEM_FOLDER/skills/<skill>/` recursively; review every `.md` |
| Whole agent system | Walk `$AGENT_SYSTEM_FOLDER/` recursively; review every `.md` plus root `AGENTS.md` |
| Recent change | Use `git diff` to identify modified `.md` files under `$AGENT_SYSTEM_FOLDER/` and root `AGENTS.md`; review each |

If no targets resolve, ask the user.

### Step 2: Identify Asset Type per File

**Goal:** Match each target file to its asset type and the matching validation checklist groups.

For each file, apply the routing table in `$AGENT_SYSTEM_SKILL/SKILL.md` § Asset-Type Routing.

If a file does not match any asset type (e.g. an arbitrary markdown doc inside the agent-system folder that is neither a SKILL.md, action, rule, checklist, AGENTS.md, nor part of a domain rules family), report it as **Unrecognised** and skip detailed validation. Do not invent a checklist for it.

### Step 3: Validate

**Goal:** Apply the matching validation groups from `$AGENT_SYSTEM_SKILL/rules/validation.md` to each file.

For every file, always apply:

- The **Universal** group
- The matching asset-type group (e.g. **Skill Manifest** for a `SKILL.md`)
- The **Cross-Cutting** group

For per-domain `devcontainer.md` or `vscode.md` files, apply the relevant sub-section of the **Domain Development-Environment Family** group (`devcontainer.md` or `vscode.md` respectively). For a domain `development-environment.md` index, apply the **Index** sub-section.

Record each item as **Pass**, **Fail**, or **N/A**. **Fail** items MUST cite the rule and the source section in the rules.

### Step 4: Report

**Goal:** Present findings as a structured review per file, with issues grouped by severity.

#### Per-File Report Structure

For each file, produce:

1. **File** — the path
2. **Asset Type** — what the file was identified as
3. **Critical** — failures that violate hard MUST rules (missing required sections, undefined variables, bare orchestrator names in rules, presence of `agents/` or `commands/` folders)
4. **Compliance** — failures that violate SHOULD rules or formatting (emojis, `---` separators, missing Quick Reference, bare paths where a variable exists, missing § notation in checklist references)
5. **Improvements** — recommendations that strengthen the asset without flagging a rule violation (clearer step goals, tighter rationale prose, missing worked-example references)
6. **Commendations** — what the file does well (brief)

#### Summary

After per-file reports, produce a one-line summary:

```
Reviewed <N> file(s): <X> Critical, <Y> Compliance, <Z> Improvements across <K> file(s) with findings.
```

If every file passes, say so explicitly: "All <N> files passed validation."

### Step 5: Recommend Next Steps

**Goal:** Tell the user what to do with the findings.

| Finding profile | Recommendation |
|-----------------|----------------|
| All Pass | No action; the asset(s) are conformant |
| Critical present | Recommend `author` (Modify mode) on the affected files; cite the failed rules |
| Compliance only | Recommend a small modify pass; group fixes by file |
| Improvements only | Surface as advisory; user decides whether to address |
| Unrecognised file(s) | Ask the user: is this file in scope for the agent system? If yes, should it move under a recognised path? If no, ignore. |

## Error Handling

| Scenario | Action |
|----------|--------|
| File does not exist at the given path | Report as **Missing**; skip; continue with remaining targets |
| File asset type cannot be determined | Report as **Unrecognised**; do not run asset-specific checks; still run Universal + Cross-Cutting |
| Validation reveals a defect in the rules themselves (a rule the rules-authority does not cover) | Report under **Improvements** as "rules gap: <description>"; surface to the user |
| Targets contain non-`.md` files | Skip them; only `.md` files are agent-system assets |
