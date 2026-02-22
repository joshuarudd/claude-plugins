---
name: setup
description: Use when first using the OST plugin in a project, or when Notion connectivity, databases, or data source configuration needs to be verified.
---

# OST Setup

Verify the OST system is properly configured and guide the user through project-level setup.

## Steps

### 1. Check for Existing Configuration

Check the project's CLAUDE.md for an `## OST Configuration` section.

- **Found** — read the configured backend and proceed to verify it (Step 3 for markdown, Step 4 for Notion).
- **Legacy config found** — if there's an `## OST Notion Data Sources` section but no `## OST Configuration`, treat as `backend: notion` and proceed to Step 4.
- **Not found** — proceed to Step 2 to choose a backend.

### 2. Choose Backend

Ask the user which backend to use:

- **Markdown** — local files as the source of truth. Best for Obsidian vaults or any markdown-based workflow. No external dependencies.
- **Notion** — uses Notion databases. Requires Notion MCP server connection and three databases (Initiatives, OST Nodes, Interviews).

Based on the choice, guide the user through the appropriate setup flow.

### 3. Markdown Backend Setup

Read `skills/setup/markdown-backend.md` from this plugin for folder conventions and schema.

#### 3a. Configure Base Path

Ask the user for the base path — the product or topic folder that will contain the `OST/` subfolder. This is relative to the vault or workspace root.

Example: `Aiwyn/Billing`

#### 3b. Obsidian Features

Ask the user if they use Obsidian:
- **Yes** — enable `obsidian-features: true` (Canvas + Base generation).
- **No** — set `obsidian-features: false` (plain markdown, fully functional).

#### 3c. Write Configuration

Guide the user to add the configuration to their project's CLAUDE.md:

```markdown
## OST Configuration
- backend: markdown
- base-path: {base-path}
- obsidian-features: true
```

#### 3d. Create Folder Structure

Verify the OST folder structure exists at `{base-path}/OST/`. If not, create it:

```
{base-path}/OST/
  Outcomes/
  Opportunities/
  Solutions/
  Experiments/
  Assumptions/
```

#### 3e. Configure Initiative

Guide the user to add an `## OST Initiatives` section to their project's CLAUDE.md:

```markdown
## OST Initiatives
- {Initiative Name}
```

For the markdown backend, the initiative name is a label used for display — the actual tree lives at the configured `base-path`.

### 4. Notion Backend Setup

Read `skills/setup/notion-backend.md` from this plugin for tool names and property mappings.

#### 4a. Verify Notion MCP Connection

Test that the Notion MCP tools are available by running a search:

```
Tool: mcp__claude_ai_Notion__notion-search
Parameters:
  query: "Initiatives"
```

If this fails, tell the user they need to connect the Notion MCP server in Claude Code and provide a link to [Notion MCP docs](https://www.notion.com/help/mcp).

#### 4b. Verify Databases Exist

Search for each database to verify access:

- **Initiatives** — search for an existing Initiative
- **OST Nodes** — search for any existing node
- **Interviews** — search for any existing interview

Report the status of each. If any are missing, describe the expected schema (reference `notion-backend.md`).

#### 4c. Discover and Configure Notion Data Source IDs

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

#### 4d. Write Configuration

If no `## OST Configuration` section exists, guide the user to add:

```markdown
## OST Configuration
- backend: notion
```

#### 4e. Check Project Initiatives

Check if the project's CLAUDE.md contains an `## OST Initiatives` section.

- **Found** — display the configured initiatives and confirm they look correct.
- **Not found** — guide the user to add it:

```markdown
## OST Initiatives
- <Initiative Name>
```

One initiative = automatic selection; multiple = prompted each time.

#### 4f. Optionally Create a New Initiative

Ask the user if they'd like to create a new Initiative. If yes:

1. Ask for: Name, Client, Status (default: Active)
2. Create in Notion using `mcp__claude_ai_Notion__notion-create-pages` with the Initiatives data source ID
3. Suggest adding it to their CLAUDE.md under `## OST Initiatives`

## Output

Summarize:
- Backend: markdown / notion
- Configuration: complete / needs setup
- For markdown: folder structure created / exists
- For Notion: connection status, databases accessible / missing, data source IDs configured / needs setup
- Project initiatives: configured / needs setup
- Next steps: suggest `/ost:set-outcomes` to define root goals
