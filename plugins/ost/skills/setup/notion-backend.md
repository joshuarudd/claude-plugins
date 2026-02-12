# Notion Backend Reference

This file contains all Notion-specific implementation details for the OST plugin. Skills reference this file for tool names, database IDs, and property mappings.

## MCP Tools

All Notion operations use the `mcp__claude_ai_Notion__*` tools:

| Operation | Tool |
|-----------|------|
| Search pages/databases | `mcp__claude_ai_Notion__notion-search` |
| Read a page | `mcp__claude_ai_Notion__notion-fetch` |
| Create pages | `mcp__claude_ai_Notion__notion-create-pages` |
| Update a page | `mcp__claude_ai_Notion__notion-update-page` |
| Update database schema | `mcp__claude_ai_Notion__notion-update-data-source` |

## Database Data Source IDs

When creating pages, use the `data_source_id` (not `database_id`).

Data source IDs are configured per-project in the project's CLAUDE.md under an `## OST Notion Data Sources` section:

```markdown
## OST Notion Data Sources
- Initiatives: <data-source-id>
- OST Nodes: <data-source-id>
- Interviews: <data-source-id>
```

Read these IDs from the project's CLAUDE.md before making any Notion API calls. If the section is missing, tell the user to run `/ost:setup` to configure it.

## Property Mappings

### Initiatives Database

| Property | Type | Notes |
|----------|------|-------|
| Name | title | e.g., "Collabrios - Color System" |
| Client | select | Client name |
| Status | select | Active / Paused / Complete |
| Start Date | date | |

### OST Nodes Database

| Property | Type | Notes |
|----------|------|-------|
| Name | title | Node label |
| Type | select | Outcome / Opportunity / Solution / Experiment |
| Initiative | relation | Relation to Initiatives database |
| Parent | relation | Self-relation to OST Nodes (creates tree hierarchy) |
| Status | select | Active / Paused / Validated / Invalidated |
| Confidence | number | Decimal 0–1 (e.g., 0.5 = 50%). Notion displays as percent. |
| Evidence Summary | rich_text | One-liner for scanning in table view |
| Source Interviews | relation | Relation to Interviews database |
| Priority | select | High / Medium / Low |
| Linked Issues | url | GitHub/Linear issue URL |

### Interviews Database

| Property | Type | Notes |
|----------|------|-------|
| Name | title | Participant name or codename |
| Initiative | relation | Relation to Initiatives (supports multiple) |
| Date | date | |
| Participant Role | select | Auto-populates |
| Company | rich_text | |
| Related Nodes | relation | Relation to OST Nodes |
| Tags | multi_select | Themes that emerged |

## Creating Pages

Use `mcp__claude_ai_Notion__notion-create-pages` with the `data_source_id` and a `pages` array. Each page has a `properties` object matching the property names above, plus an optional `body` for page content.

### Example: Create an OST Node

```
Tool: mcp__claude_ai_Notion__notion-create-pages
Parameters:
  data_source_id: "<ost-nodes-data-source-id>"
  pages: [{
    "properties": {
      "Name": "Users struggle to find settings",
      "Type": "Opportunity",
      "Initiative": "<initiative-page-url>",
      "Status": "Active",
      "Confidence": 0.5,
      "Evidence Summary": "3 users mentioned difficulty finding settings in interviews"
    },
    "body": "## Evidence\n\n- Interview with P1: \"I couldn't find where to change my notification preferences\"\n- Interview with P2: \"The settings menu is buried too deep\""
  }]
```

### Example: Create an Interview

```
Tool: mcp__claude_ai_Notion__notion-create-pages
Parameters:
  data_source_id: "<interviews-data-source-id>"
  pages: [{
    "properties": {
      "Name": "P1 - Product Manager",
      "Initiative": ["<initiative-page-url>"],
      "Date": "2026-02-11",
      "Participant Role": "Product Manager",
      "Company": "Acme Corp",
      "Tags": ["onboarding", "settings"]
    },
    "body": "## Transcript\n\n[Full transcript text here]"
  }]
```

## Relations

Relations are set as Notion page URL strings when creating pages. To link an OST Node to an Initiative:

```json
"Initiative": "https://www.notion.so/Initiative-Page-Id"
```

Note: The Notion MCP `create-pages` tool accepts a single URL string per relation property. To link to multiple pages (e.g., an Interview spanning multiple Initiatives), create with one relation, then use `notion-update-page` to add additional relations.

To find page URLs, use `mcp__claude_ai_Notion__notion-search` and extract URLs from the results.

## Querying

### Search for OST Nodes by Initiative

Use `mcp__claude_ai_Notion__notion-search` with a query matching the Initiative name or node content. Filter results by checking the Initiative relation property.

### Fetch a specific page

Use `mcp__claude_ai_Notion__notion-fetch` with the page URL to read full page content including the body.

## Known Limitations

- Select/multi_select options must already exist in the database schema before creating pages that use new option values. Use `mcp__claude_ai_Notion__notion-update-data-source` to add new options if needed.
- Self-relations (Parent on OST Nodes) work normally — just pass the page URL of the parent node.
- The reverse relation from Interviews to OST Nodes may appear as "Related Nodes" in Notion UI rather than "Source Interviews".
