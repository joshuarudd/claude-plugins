---
name: process-transcript
description: Use when the user provides a full interview transcript, meeting notes, or interview recording summary to be processed into OST nodes.
---

# Process Transcript

Process an interview transcript into Notion — creating an Interview entry and extracting opportunities — isolated from the main context using the Task tool.

## Workflow

1. Resolve Initiative and load Notion config. See `skills/setup/initiative-resolution.md`.
2. Gather interview metadata: participant name/codename, role, company, date. Ask the user if not apparent from the transcript.
3. Identify themes/tags from the transcript content.
4. Spawn a Task tool sub-agent (`subagent_type: "general-purpose"`) with the instructions below.
5. Confirm what was created/updated: the Interview entry and each OST Node.

## Task Tool Instructions

### Context to include

- The full transcript text (or file contents if a path was provided)
- The resolved Initiative name(s) and Notion page URLs
- Interview metadata: participant name, role, company, date
- Extracted themes/tags
- The Notion data source IDs from the project's CLAUDE.md
- The full contents of `skills/setup/notion-backend.md` from this plugin

### Sub-agent steps

1. Load data source IDs and Notion backend reference. If data sources missing, stop and tell user to run `/ost:setup`.
2. Search for the Initiative's page URL.
3. **Create the Interview entry**: Name (participant), Initiative link, Date, Participant Role, Company, Tags, page body with full transcript.
4. **Extract opportunities** from the transcript:
   - Pain points and frustrations
   - Unmet needs or desires
   - Workarounds created by participant
   - Moments of confusion or difficulty
   - Feature requests (reframe as underlying need)
5. For each extracted opportunity:
   a. Search existing OST Nodes for a **semantic match**.
   b. **Match found** — append evidence, add quotes to page body, link Interview as Source Interview.
   c. **No match** — create new Opportunity node: Name (user-problem framing), Type=Opportunity, Initiative link, Status=Active, Confidence=50, Evidence Summary, Source Interviews link, page body with quotes.
6. Return summary: Interview entry name/URL, each opportunity (new vs updated) with key evidence.

## Input

Transcript from `$ARGUMENTS` (pasted text or file path). If a file path, read contents first. If no arguments, check conversation context.
