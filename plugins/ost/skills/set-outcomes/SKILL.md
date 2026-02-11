---
name: set-outcomes
description: Define the key Outcomes (root nodes) for an initiative. Outcomes are the top-level goals that Opportunities, Solutions, and Experiments ladder up to. Use when the user wants to establish, view, or modify the root outcomes of their Opportunity Solution Tree.
argument-hint: <outcome descriptions (optional)>
---

# Set Outcomes

Define or modify the root Outcome nodes for an initiative's Opportunity Solution Tree.

## Workflow

1. Resolve which Initiative this applies to (see Initiative Resolution below).
2. Read data source IDs from the project's CLAUDE.md `## OST Notion Data Sources` section. If missing, tell the user to run `/ost:setup` first.
3. Read `backends/notion.md` from this plugin for Notion tool names and property mappings.
4. Query existing Outcome nodes for this Initiative (Type=Outcome, no Parent).
5. If outcomes already exist, display them and ask the user how to proceed.
6. Create or modify Outcome nodes as directed.
7. Summarize what was created or updated.

## Initiative Resolution

Check the current project's CLAUDE.md for an `## OST Initiatives` section:

- **One initiative listed** — use it automatically.
- **Multiple listed** — if `$ARGUMENTS` names one, use it; otherwise ask.
- **None listed** — query Notion for Active initiatives, ask the user to pick.

## Steps

### 1. Find Existing Outcomes

Search for OST Nodes linked to the resolved Initiative using `mcp__claude_ai_Notion__notion-search`. Filter results to nodes where Type=Outcome and Parent is empty (root nodes).

### 2. Present Current State

- **Outcomes exist** — display them in a numbered list with their Status and Confidence, then ask the user:
  - **Add more** — keep existing outcomes and add new ones
  - **Modify** — update the name, status, or confidence of specific outcomes
  - **Replace** — remove existing outcomes and define new ones (confirm before deleting)
- **No outcomes yet** — proceed directly to creating new ones.

### 3. Gather Outcome Descriptions

- If `$ARGUMENTS` contains outcome descriptions, parse them (split on newlines or numbered list items).
- If no arguments, ask the user to describe their key outcomes. Prompt with guidance:
  - Outcomes should be measurable product or business goals
  - Examples: "Increase user activation rate", "Reduce time to first value", "Improve team collaboration efficiency"
  - Typically 1-3 outcomes per initiative

### 4. Create Outcome Nodes

For each new outcome, create an OST Node in Notion:

- **Name**: the outcome description
- **Type**: Outcome
- **Initiative**: link to the Initiative page URL
- **Status**: Active
- **Confidence**: 50
- **Parent**: (empty — outcomes are root nodes)

Use `mcp__claude_ai_Notion__notion-create-pages` with the OST Nodes data source ID from the project's CLAUDE.md.

### 5. Summarize

Report what was created or modified:
- List each outcome with its name and status
- Suggest next steps: use `/ost:capture-feedback` or `/ost:process-transcript` to start adding Opportunities under these Outcomes

## Input

Outcome descriptions come from `$ARGUMENTS` or interactive input if no arguments are provided.
