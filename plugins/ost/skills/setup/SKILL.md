---
name: setup
description: Use when first using the OST plugin in a project, or when Notion connectivity, databases, or data source configuration needs to be verified.
---

# OST Setup

Verify the OST system is properly configured and guide the user through project-level setup.

## Steps

### 1. Verify Notion MCP Connection

Test that the Notion MCP tools are available by running a search:

```
Tool: mcp__claude_ai_Notion__notion-search
Parameters:
  query: "Initiatives"
```

If this fails, tell the user they need to connect the Notion MCP server in Claude Code and provide a link to [Notion MCP docs](https://www.notion.com/help/mcp).

### 2. Verify Databases Exist

Search for each database to verify access:

- **Initiatives** — search for an existing Initiative
- **OST Nodes** — search for any existing node
- **Interviews** — search for any existing interview

Report the status of each. If any are missing, describe the expected schema (reference `notion-backend.md` in this skill directory).

### 3. Discover and Configure Notion Data Source IDs

Check if the project's CLAUDE.md contains an `## OST Notion Data Sources` section.

- **Found** — read the configured IDs and verify they work.
- **Not found** — discover automatically:
  1. Search for databases named "Initiatives", "OST Nodes", and "Interviews".
  2. Extract the data source ID from each result.
  3. Present discovered IDs for confirmation.
  4. Guide the user to add the section to their project's CLAUDE.md:

```markdown
## OST Notion Data Sources
- Initiatives: <discovered-id>
- OST Nodes: <discovered-id>
- Interviews: <discovered-id>
```

These IDs are workspace-specific and required for all OST skills.

### 4. Check Project Initiatives

Check if the project's CLAUDE.md contains an `## OST Initiatives` section.

- **Found** — display the configured initiatives and confirm they look correct.
- **Not found** — guide the user to add it:

```markdown
## OST Initiatives
- <Initiative Name>
```

One initiative = automatic selection; multiple = prompted each time.

### 5. Optionally Create a New Initiative

Ask the user if they'd like to create a new Initiative. If yes:

1. Ask for: Name, Client, Status (default: Active)
2. Create in Notion using `mcp__claude_ai_Notion__notion-create-pages` with the Initiatives data source ID
3. Suggest adding it to their CLAUDE.md under `## OST Initiatives`

## Output

Summarize:
- Notion connection: connected / not connected
- Databases: accessible / missing
- Data source IDs: configured / needs setup
- Project initiatives: configured / needs setup
- Next steps
