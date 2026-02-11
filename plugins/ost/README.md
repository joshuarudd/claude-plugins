# OST — Opportunity Solution Tree Plugin

Capture feedback, process interview transcripts, and map opportunities using the Opportunity Solution Tree framework from Teresa Torres' *Continuous Discovery Habits*. Uses Notion as the data layer.

## Prerequisites

- Notion MCP server connected in Claude Code
- Three Notion databases: Initiatives, OST Nodes, Interviews (see `backends/notion.md` for schema)

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| Setup | `/ost:setup` | Verify configuration and onboard |
| Set Outcomes | `/ost:set-outcomes [outcomes]` | Define root Outcome nodes for an initiative |
| Capture Feedback | `/ost:capture-feedback <text>` | Map feedback to Opportunity nodes |
| Process Transcript | `/ost:process-transcript <text or path>` | Create Interview + extract opportunities |
| List Opportunities | `/ost:list-opportunities [initiative]` | Display the OST hierarchy |

## Per-Project Configuration

Add these sections to your project's CLAUDE.md (or run `/ost:setup` to be guided through it):

```markdown
## OST Initiatives
- My Initiative Name

## OST Notion Data Sources
- Initiatives: <data-source-id>
- OST Nodes: <data-source-id>
- Interviews: <data-source-id>
```

**Initiatives**: One listed = used automatically. Multiple = you'll be prompted. None = queries Notion.

**Data Sources**: Workspace-specific Notion IDs required for all skills. Run `/ost:setup` to discover them automatically.

## Installation

Add the marketplace, then install the plugin:

```
claude mcp add-marketplace joshuarudd/claude-plugins
claude plugin install ost
```

Or test locally:

```
claude --plugin-dir ./plugins/ost
```
