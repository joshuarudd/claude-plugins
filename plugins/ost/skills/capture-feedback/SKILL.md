---
name: capture-feedback
description: Capture user feedback, observations, or insights and map them to Opportunity Solution Tree nodes in Notion. Use when the user provides feedback, interview quotes, observations, or insights that should be tracked as opportunities. Supports single and batch capture — automatically determines the number of discrete opportunities from the feedback provided.
argument-hint: <feedback text>
---

# Capture Feedback

Capture feedback and map it to OST Opportunity nodes in Notion, isolated from the main working context using the Task tool.

## Workflow

1. Resolve which Initiative(s) this feedback belongs to (see Initiative Resolution below).
2. Analyze the feedback to identify distinct opportunities or insights. Each separate user problem, unmet need, or observation is a distinct opportunity.
3. Spawn a Task tool sub-agent with the instructions below.
4. Confirm what was created/updated with node names and a brief summary. Keep confirmation concise.

## Initiative Resolution

Check the current project's CLAUDE.md for an `## OST Initiatives` section:

- **One initiative listed** — use it automatically, no need to ask.
- **Multiple listed** — ask the user which one(s) this feedback applies to.
- **None listed** — query Notion for Active initiatives using `mcp__claude_ai_Notion__notion-search`, then ask the user to pick.

## Task Tool Instructions

Pass the following to the Task tool sub-agent (use `subagent_type: "general-purpose"`):

### Context to include

- The parsed feedback (separated into discrete opportunities if multiple)
- The resolved Initiative name(s) and their Notion page URLs
- The Notion data source IDs read from the project's CLAUDE.md `## OST Notion Data Sources` section
- The full contents of `backends/notion.md` from this plugin (read it and include the Notion tool names and property mappings)

### Sub-agent steps

1. Read data source IDs from the project's CLAUDE.md `## OST Notion Data Sources` section. Read `backends/notion.md` from this plugin for Notion tool names and property mappings. If the data sources section is missing, stop and tell the user to run `/ost:setup` first.
2. For each Initiative, search existing OST Nodes to find the Initiative's page URL using `mcp__claude_ai_Notion__notion-search`.
3. For each discrete opportunity extracted from the feedback:
   a. Search existing OST Nodes for this Initiative for a **semantic match** — look for nodes that describe the same underlying user problem or need, even if worded differently.
   b. **Match found** — update the existing node:
      - Append to the Evidence Summary with the new evidence.
      - Add the new evidence details to the page body using `mcp__claude_ai_Notion__notion-update-page`.
   c. **No match** — create a new Opportunity node:
      - Name: concise description of the opportunity (user-problem framing)
      - Type: Opportunity
      - Initiative: link to the Initiative page URL
      - Status: Active
      - Confidence: 50
      - Evidence Summary: one-line summary of the evidence
      - Page body: full evidence text with context
4. Return a summary of what was created and updated: node names, whether new or updated, and the key evidence captured.

## Parsing Feedback into Opportunities

Use judgment to separate feedback into discrete opportunities. Signals for splitting:

- Different user problems or pain points
- Different parts of the product or workflow mentioned
- Separate unmet needs joined by "also," "and another thing," or similar

When ambiguous, prefer fewer opportunities with broader scope. The user can always split them later.

Opportunities should be framed as user problems or needs, not solutions. For example:
- Good: "Users struggle to find notification settings"
- Bad: "Add a settings shortcut to the header"

## Input

The feedback text comes from `$ARGUMENTS` or from the conversation context if no arguments are provided.
