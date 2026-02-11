---
name: setup
description: Set up the OST plugin for a project. Verifies Notion connectivity, checks that the required databases exist, and guides the user to configure their project's CLAUDE.md with Initiative references. Run this when first using the OST plugin in a new project.
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

Report the status of each database. If any are missing, inform the user that the OST Notion databases need to be created first and describe the expected schema (reference `backends/notion.md` from this plugin).

### 3. Discover and Configure Notion Data Source IDs

Check if the current project's CLAUDE.md contains an `## OST Notion Data Sources` section.

- **Found** — read the configured data source IDs and verify they work by attempting a search against each.
- **Not found** — discover the IDs automatically:
  1. Use `mcp__claude_ai_Notion__notion-search` to search for databases named "Initiatives", "OST Nodes", and "Interviews".
  2. Extract the data source ID from each search result.
  3. Present the discovered IDs to the user for confirmation.
  4. Guide the user to add the section to their project's CLAUDE.md:

```markdown
## OST Notion Data Sources
- Initiatives: <discovered-id>
- OST Nodes: <discovered-id>
- Interviews: <discovered-id>
```

Tell them these IDs are workspace-specific and required for all OST skills to function.

### 4. Check Project Initiatives

Check if the current project's CLAUDE.md contains an `## OST Initiatives` section.

- **Found** — display the configured initiatives and confirm they look correct.
- **Not found** — guide the user to add the section:

```markdown
## OST Initiatives
- <Initiative Name>
```

Tell them:
- List one initiative per line with a `- ` prefix
- Use the exact name as it appears in the Notion Initiatives database
- One initiative = automatic selection; multiple = prompted each time

### 5. Optionally Create a New Initiative

Ask the user if they'd like to create a new Initiative for this project. If yes:

1. Ask for: Name, Client, Status (default: Active)
2. Read the Initiatives data source ID from the project's CLAUDE.md (configured in step 3)
3. Create the Initiative in Notion using `mcp__claude_ai_Notion__notion-create-pages` with that data source ID
4. Suggest adding it to their CLAUDE.md under `## OST Initiatives`

## Output

Provide a summary:
- Notion connection: connected / not connected
- Databases: accessible / missing (with details)
- Data source IDs: configured / needs setup
- Project initiatives: configured / needs setup
- Next steps: what the user should do to start using the OST skills
