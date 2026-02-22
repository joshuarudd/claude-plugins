---
name: set-outcomes
description: Use when the user wants to establish, view, or modify the root Outcome nodes of an initiative's Opportunity Solution Tree.
---

# Set Outcomes

Define or modify the root Outcome nodes for an initiative's OST.

## Workflow

1. Resolve initiative and load backend config. See `skills/setup/initiative-resolution.md`.
2. Query existing Outcome nodes (root nodes with no parent).
3. If outcomes exist, display them and ask the user how to proceed.
4. Create or modify Outcome nodes as directed.
5. Summarize what was created or updated.

## Steps

### 1. Find Existing Outcomes

#### Markdown backend

1. Read `base-path` from `## OST Configuration` in the project's CLAUDE.md.
2. Glob `{base-path}/OST/Outcomes/*.md`.
3. Read each file's frontmatter to get name, status, and confidence.

#### Notion backend

Search for OST Nodes linked to the resolved Initiative. Filter to Type=Outcome and Parent is empty (root nodes).

### 2. Present Current State

- **Outcomes exist** — display in a numbered list with Status and Confidence, then ask:
  - **Add more** — keep existing, add new
  - **Modify** — update name, status, or confidence of specific outcomes
  - **Replace** — remove existing and define new (confirm before deleting)
- **No outcomes yet** — proceed to creating new ones.

### 3. Gather Outcome Descriptions

- If `$ARGUMENTS` contains descriptions, parse them (split on newlines or numbered items).
- Otherwise ask the user. Guidance:
  - Outcomes should be measurable product or business goals
  - Examples: "Increase user activation rate", "Reduce time to first value"
  - Typically 1-3 outcomes per initiative

### 4. Create Outcome Nodes

#### Markdown backend

For each outcome, write a new file in `{base-path}/OST/Outcomes/`:

- **Filename**: `{Outcome description}.md`
- **Frontmatter**: `ost-type: Outcome`, `status: Active`, `confidence: 50`, `created-date: {today}`
- **No `parent`** field (root nodes)

Reference `skills/setup/markdown-backend.md` for the full frontmatter schema.

#### Notion backend

For each outcome, create an OST Node:

- **Name**: the outcome description
- **Type**: Outcome
- **Initiative**: link to Initiative page URL
- **Status**: Active
- **Confidence**: 0.5
- **Parent**: empty (root nodes)

Reference `skills/setup/notion-backend.md` for tool names and property mappings.

### 5. Summarize

Report what was created/modified. Suggest next steps: `/ost:capture-feedback` or `/ost:analyze-transcript` to start adding Opportunities.

## Input

Outcome descriptions from `$ARGUMENTS` or interactive input.
