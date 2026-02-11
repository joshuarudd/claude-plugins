---
name: process-transcript
description: Process an interview transcript or meeting notes into the Opportunity Solution Tree. Creates an Interview entry in Notion and extracts opportunities, linking evidence to existing or new OST Nodes. Use when the user provides a full transcript, meeting notes, or interview recording summary.
argument-hint: <transcript text or file path>
---

# Process Transcript

Process an interview transcript into Notion — creating an Interview entry and extracting opportunities — isolated from the main context using the Task tool.

## Workflow

1. Resolve which Initiative(s) this transcript belongs to (see Initiative Resolution below).
2. Gather interview metadata: participant name/codename, role, company, date. Ask the user if not apparent from the transcript.
3. Identify themes/tags from the transcript content.
4. Spawn a Task tool sub-agent with the instructions below.
5. Confirm what was created/updated: the Interview entry and each OST Node (new or updated). Keep confirmation concise.

## Initiative Resolution

Check the current project's CLAUDE.md for an `## OST Initiatives` section:

- **One initiative listed** — use it automatically, no need to ask.
- **Multiple listed** — ask the user which one(s) this transcript applies to.
- **None listed** — query Notion for Active initiatives using `mcp__claude_ai_Notion__notion-search`, then ask the user to pick.

## Task Tool Instructions

Pass the following to the Task tool sub-agent (use `subagent_type: "general-purpose"`):

### Context to include

- The full transcript text (or file contents if a path was provided)
- The resolved Initiative name(s) and their Notion page URLs
- Interview metadata: participant name, role, company, date
- Extracted themes/tags
- The Notion data source IDs read from the project's CLAUDE.md `## OST Notion Data Sources` section
- The full contents of `backends/notion.md` from this plugin (for Notion tool names and property mappings)

### Sub-agent steps

1. Read data source IDs from the project's CLAUDE.md `## OST Notion Data Sources` section. Read `backends/notion.md` from this plugin for Notion tool names and property mappings. If the data sources section is missing, stop and tell the user to run `/ost:setup` first.
2. Search for the Initiative's page URL using `mcp__claude_ai_Notion__notion-search`.
3. **Create the Interview entry** in Notion:
   - Name: participant name or codename
   - Initiative: link to Initiative page URL(s)
   - Date: interview date
   - Participant Role: role (if known)
   - Company: company name (if known)
   - Tags: themes extracted from the transcript
   - Page body: the full transcript text
4. **Extract opportunities** from the transcript. Look for:
   - Pain points and frustrations expressed by the participant
   - Unmet needs or desires
   - Workarounds the participant has created
   - Moments of confusion or difficulty
   - Feature requests (reframe as the underlying need)
5. For each extracted opportunity:
   a. Search existing OST Nodes for this Initiative for a **semantic match**.
   b. **Match found** — update the existing node:
      - Append to Evidence Summary with new evidence.
      - Add interview quotes and context to the page body.
      - Link the Interview as a Source Interview (add to the Source Interviews relation).
   c. **No match** — create a new Opportunity node:
      - Name: concise user-problem framing
      - Type: Opportunity
      - Initiative: link to Initiative page URL
      - Status: Active
      - Confidence: 50
      - Evidence Summary: one-line summary
      - Source Interviews: link to the newly created Interview entry
      - Page body: relevant quotes and context from the transcript
6. Return a summary: the Interview entry name/URL, and for each opportunity whether it was new or updated, with the key evidence.

## Input

The transcript comes from `$ARGUMENTS` (pasted text or a file path). If a file path is provided, read the file contents first. If no arguments are provided, check the conversation context for transcript text.
