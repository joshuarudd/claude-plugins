---
name: capture-feedback
description: Use when the user provides feedback, interview quotes, observations, or insights that should be tracked as OST Opportunity nodes.
---

# Capture Feedback

Capture feedback and map it to OST Opportunity nodes in Notion, isolated from the main context using the Task tool.

## Workflow

1. Resolve Initiative and load Notion config. See `skills/setup/initiative-resolution.md`.
2. Analyze feedback to identify distinct opportunities (each separate user problem or unmet need is one).
3. Spawn a Task tool sub-agent (`subagent_type: "general-purpose"`) with the instructions below.
4. Confirm what was created/updated with node names and a brief summary.

## Task Tool Instructions

### Context to include

- The parsed feedback (separated into discrete opportunities if multiple)
- The resolved Initiative name(s) and Notion page URLs
- The Notion data source IDs from the project's CLAUDE.md
- The full contents of `skills/setup/notion-backend.md` from this plugin

### Sub-agent steps

1. Load data source IDs and Notion backend reference. If data sources missing, stop and tell user to run `/ost:setup`.
2. Search existing OST Nodes to find the Initiative's page URL.
3. For each discrete opportunity:
   a. Search existing OST Nodes for a **semantic match** — same underlying user problem, even if worded differently.
   b. **Match found** — append to Evidence Summary and add details to page body.
   c. **No match** — create a new Opportunity node: Name (user-problem framing), Type=Opportunity, Initiative link, Status=Active, Confidence=50, Evidence Summary, page body with full context.
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
