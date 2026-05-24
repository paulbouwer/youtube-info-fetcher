# Decision Records

This folder contains decision records (DRs) documenting significant decisions made for this project.

## Purpose

Decision records capture the context, rationale, and consequences of important technical and architectural decisions. They serve as:

- **Historical documentation** — Why decisions were made
- **Onboarding aid** — Help new contributors understand the project
- **Change management** — Track evolution of architecture over time

## Structure

```text
$PROJECT_FOLDER/decision-records/
├── README.md                           # This file
├── YYYY-MM-DD-short-description.md     # Active decision records
└── deprecated/                         # Deprecated or superseded records
```

## Creating Decision Records

Use the **decision-records** skill to create or review decision records:

```text
/decision-records create a decision record for <topic>
/decision-records review the decision record at <decision-record-file>
```

The skill guides you through Socratic discovery to ensure decisions are well-understood before documentation, and automatically maintains the [decision-records.index.md](decision-records.index.md).

### Quick Reference

| Item               | Rule                                                               |
| ------------------ | ------------------------------------------------------------------ |
| **Filename**       | `YYYY-MM-DD-short-description.md` (lowercase, hyphens)             |
| **Initial Status** | `Proposed`                                                         |
| **Required**       | Y-Statement summary, context, decision, consequences, alternatives |

## Status Lifecycle

| Status         | Description                                             |
| -------------- | ------------------------------------------------------- |
| **Proposed**   | Under discussion; not yet approved                      |
| **Accepted**   | Approved and should be followed                         |
| **Deprecated** | No longer recommended; moved to `deprecated/`           |
| **Superseded** | Replaced by newer decision record (link to replacement) |

## Index

See [decision-records.index.md](decision-records.index.md) for a quick reference of all decision records with their status and Y-Statement summary.
