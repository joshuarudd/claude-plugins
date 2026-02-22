---
name: analyze-transcript
description: Use when the user provides a full interview transcript, meeting notes, or interview recording summary to be analyzed for OST opportunities.
---

# Analyze Transcript

Systematically analyze an existing source document (transcript, meeting notes) for opportunities, creating or updating OST nodes. Isolated from the main context using the Task tool.

**Key difference from `capture-feedback`:** This skill analyzes an entire document systematically, extracting all opportunities. `capture-feedback` handles targeted, discrete insights.

**Important:** This skill does not create transcript or interview files. The source document already exists — the skill reads it, extracts opportunities, and links OST nodes back to the source.

## Workflow

1. Resolve initiative and load backend config. See `skills/setup/initiative-resolution.md`.
2. Gather source document: read the file at the provided path, or use pasted text from `$ARGUMENTS`.
3. Gather interview metadata if apparent: participant name/codename, role, company, date. Ask the user if not clear from the content.
4. Spawn a Task tool sub-agent (`subagent_type: "general-purpose"`) with the instructions below.
5. Confirm what was created/updated: each opportunity (new vs updated) with key evidence.

## Task Tool Instructions

### Context to include

- The full source document text
- The resolved initiative name
- Interview metadata (participant, role, company, date) if available
- The backend type and configuration details
- The full contents of the appropriate backend reference file (`skills/setup/markdown-backend.md` or `skills/setup/notion-backend.md`)

#### Markdown-specific context

- The `base-path` from `## OST Configuration`
- The OST folder path: `{base-path}/OST/`

#### Notion-specific context

- The resolved initiative Notion page URL
- The Notion data source IDs from the project's CLAUDE.md

### Sub-agent steps

#### Markdown backend

1. Load backend config. If `base-path` is missing, stop and tell user to run `/ost:setup`.
2. **Extract opportunities** from the source document:
   - Pain points and frustrations
   - Unmet needs or desires
   - Workarounds created by participant
   - Moments of confusion or difficulty
   - Feature requests (reframe as underlying need)
3. Glob `{base-path}/OST/Opportunities/*.md` to find existing Opportunity nodes.
4. For each extracted opportunity:
   a. Read existing nodes' frontmatter and body. Search for a **semantic match** — same underlying user problem, even if worded differently.
   b. **Match found** — update the existing file:
      - Append to `evidence-summary`
      - Add source wikilink to `source-interviews` list in frontmatter
      - Add new evidence quotes to the body
      - Update `confidence` if warranted (more evidence = higher confidence)
   c. **No match** — create a new `.md` file in `{base-path}/OST/Opportunities/`:
      - Filename: `{User problem description}.md`
      - Frontmatter: `ost-type: Opportunity`, `status: Active`, `confidence: 50`, `parent` (link to most relevant Outcome if obvious), `evidence-summary`, `source-interviews` with wikilink to source document, `created-date`
      - Body: `## Evidence` section with relevant quotes from the source
5. Return summary: each opportunity (new vs updated) with key evidence captured.

#### Notion backend

1. Load data source IDs and Notion backend reference. If data sources missing, stop and tell user to run `/ost:setup`.
2. Search for the Initiative's page URL.
3. **Extract opportunities** from the source document (same extraction as markdown backend).
4. For each extracted opportunity:
   a. Search existing OST Nodes for a **semantic match**.
   b. **Match found** — append evidence, add quotes to page body, link source document if an Interview entry exists.
   c. **No match** — create new Opportunity node: Name (user-problem framing), Type=Opportunity, Initiative link, Status=Active, Confidence=0.5, Evidence Summary, Source Interviews link if applicable, page body with quotes.
5. Return summary: each opportunity (new vs updated) with key evidence.

## Extraction Guidelines

- Extract **user problems**, not solutions. Reframe feature requests as the underlying need.
- Group related pain points into a single opportunity when they describe the same core problem.
- Include direct quotes as evidence — they're more compelling than paraphrases.
- Note the frequency: if the same issue comes up multiple times in one interview, note that.

## Input

Source document from `$ARGUMENTS` (pasted text or file path). If a file path, read contents first. If no arguments, check conversation context.
