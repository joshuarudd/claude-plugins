# OST — Opportunity Solution Tree Plugin

Capture feedback, analyze interview transcripts, and map opportunities using the Opportunity Solution Tree framework from Teresa Torres' *Continuous Discovery Habits*.

Supports two backends:
- **Markdown** — local files as the source of truth, with optional Obsidian enhancements (Canvas, Bases)
- **Notion** — Notion databases via MCP

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| Setup | `/ost:setup` | Choose backend, configure paths, verify access |
| Set Outcomes | `/ost:set-outcomes [outcomes]` | Define root Outcome nodes for an initiative |
| Capture Feedback | `/ost:capture-feedback <text>` | Map feedback to Opportunity nodes |
| Analyze Transcript | `/ost:analyze-transcript <text or path>` | Extract opportunities from a source document |
| List Opportunities | `/ost:list-opportunities [initiative]` | Display the OST hierarchy |

## Backends

### Markdown Backend

Local `.md` files with YAML frontmatter. No external dependencies.

**Folder structure:**
```
{base-path}/
  OST/
    Outcomes/
    Opportunities/
    Solutions/
    Experiments/
    Assumptions/
    OST.canvas        (Obsidian only)
    OST.base           (Obsidian only)
```

Each node is a markdown file with frontmatter (`ost-type`, `status`, `confidence`, `parent`, `evidence-summary`, `source-interviews`, `created-date`). Tree hierarchy is built via `parent` wikilinks.

**Obsidian features** (optional): Canvas for interactive tree visualization, Base for tabular database view. Enabled via `obsidian-features: true` in config.

**Configuration in CLAUDE.md:**
```markdown
## OST Configuration
- backend: markdown
- base-path: Aiwyn/Billing
- obsidian-features: true

## OST Initiatives
- Billing
```

### Notion Backend

Uses three Notion databases: Initiatives, OST Nodes, Interviews. Requires Notion MCP server connected in Claude Code.

See `skills/setup/notion-backend.md` for database schema.

**Configuration in CLAUDE.md:**
```markdown
## OST Configuration
- backend: notion

## OST Initiatives
- My Initiative Name

## OST Notion Data Sources
- Initiatives: <data-source-id>
- OST Nodes: <data-source-id>
- Interviews: <data-source-id>
```

## Per-Project Configuration

Run `/ost:setup` to be guided through configuration, or add the sections to your project's CLAUDE.md manually.

**Initiatives**: One listed = used automatically. Multiple = you'll be prompted. None = queries backend.

## Installation

Test locally:

```
claude --plugin-dir ./plugins/ost
```
