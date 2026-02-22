# Backend Resolution

Shared pattern used by all OST skills. Determines which backend is configured and loads the appropriate reference file.

## Steps

### 1. Read Backend Configuration

Check the project's CLAUDE.md for an `## OST Configuration` section:

```markdown
## OST Configuration
- backend: markdown
- obsidian-features: true
```

or:

```markdown
## OST Configuration
- backend: notion
```

- **Found `backend: markdown`** — use the markdown backend. Read `skills/setup/markdown-backend.md` from this plugin for file operations, frontmatter schema, and folder conventions.
- **Found `backend: notion`** — use the Notion backend. Read `skills/setup/notion-backend.md` from this plugin for tool names and property mappings. Also read data source IDs from `## OST Notion Data Sources`.
- **No `## OST Configuration` section** — check for a legacy `## OST Notion Data Sources` section. If found, treat as `backend: notion`. If neither section exists, tell the user to run `/ost:setup` to configure a backend.

### 2. Load Backend Reference

Based on the resolved backend, read the corresponding reference file from this plugin's `skills/setup/` directory:

| Backend | Reference file | Key contents |
|---------|---------------|--------------|
| `markdown` | `markdown-backend.md` | File operations, frontmatter schema, folder conventions, search patterns |
| `notion` | `notion-backend.md` | MCP tool names, property mappings, data source patterns |

### Important: Confidence Scale Difference

The two backends use different confidence scales:

- **Markdown**: integer 0–100 (e.g., `confidence: 50`)
- **Notion**: decimal 0–1 (e.g., `Confidence: 0.5`). Notion displays this as a percentage automatically.

Always use the correct scale for the active backend.

### 3. Proceed to Initiative Resolution

After loading the backend reference, proceed to `skills/setup/initiative-resolution.md` to resolve the target initiative. Initiative resolution is backend-aware — it uses different strategies depending on the backend.
