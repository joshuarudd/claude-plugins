---
name: capture-feedback
description: Use when the user provides feedback, interview quotes, observations, or insights that should be tracked as OST Opportunity nodes.
---

# Capture Feedback

Capture feedback and map it to OST Opportunity nodes, isolated from the main context using the Task tool.

## Workflow

1. Resolve initiative and load backend config. See `skills/setup/initiative-resolution.md`.
2. Analyze feedback to identify distinct opportunities (each separate user problem or unmet need is one).
3. Spawn a Task tool sub-agent (`subagent_type: "general-purpose"`) with the instructions below.
4. Confirm what was created/updated with node names and a brief summary.

## Task Tool Instructions

### Context to include

- The parsed feedback (separated into discrete opportunities if multiple)
- The resolved initiative name
- The backend type and configuration details
- The full contents of the appropriate backend reference file (`skills/setup/markdown-backend.md` or `skills/setup/notion-backend.md`)

#### Markdown-specific context

- The resolved initiative path (from initiative resolution)
- The OST folder path: `{initiative-path}/OST/`

#### Notion-specific context

- The resolved initiative Notion page URL
- The Notion data source IDs from the project's CLAUDE.md

### Sub-agent steps

#### Markdown backend

1. Use the resolved initiative path. If missing, stop and tell user to run `/ost:setup`.
2. Glob `{initiative-path}/OST/Opportunities/*.md` to find existing Opportunity nodes.
3. For each discrete opportunity:
   a. Read existing nodes' frontmatter and body. Search for a **semantic match** — same underlying user problem, even if worded differently.
   b. **Match found** — update the existing file: append to `evidence-summary`, add new evidence quotes to the body, update `confidence` if warranted.
   c. **No match** — create a new `.md` file in `{initiative-path}/OST/Opportunities/`:
      - Filename: `{User problem description}.md`
      - Frontmatter: `ost-type: Opportunity`, `status: Active`, `confidence: 50`, `parent` (link to most relevant Outcome if obvious, otherwise omit), `evidence-summary`, `created-date`
      - Body: `## Evidence` section with the feedback quotes/context.
4. Return summary: node names, new vs updated, key evidence captured.

#### Notion backend

1. Load data source IDs and Notion backend reference. If data sources missing, stop and tell user to run `/ost:setup`.
2. Search existing OST Nodes to find the Initiative's page URL.
3. For each discrete opportunity:
   a. Search existing OST Nodes for a **semantic match** — same underlying user problem, even if worded differently.
   b. **Match found** — append to Evidence Summary and add details to page body.
   c. **No match** — create a new Opportunity node: Name (user-problem framing), Type=Opportunity, Initiative link, Status=Active, Confidence=0.5, Evidence Summary, page body with full context.
4. Return summary: node names, new vs updated, key evidence captured.

## Parsing Feedback into Opportunities

Signals for splitting:
- Different user problems or pain points
- Different product areas or workflows
- Separate needs joined by "also" or "and another thing"

When ambiguous, prefer fewer opportunities with broader scope.

Frame as user problems, not solutions:
- Good: "Users struggle to find notification settings"
- Bad: "Add a settings shortcut to the header"

## Input

Feedback from `$ARGUMENTS` or conversation context.
