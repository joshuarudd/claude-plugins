# Initiative Resolution

Shared pattern used by all OST skills that operate on a specific Initiative.

## Steps

1. Read data source IDs from the project's CLAUDE.md `## OST Notion Data Sources` section. If missing, tell the user to run `/ost:setup` first.
2. Read `skills/setup/notion-backend.md` from this plugin for Notion tool names and property mappings.
3. Resolve the target Initiative:

Check the project's CLAUDE.md for an `## OST Initiatives` section:

- **One initiative listed** — use it automatically.
- **Multiple listed** — if `$ARGUMENTS` names one, use it; otherwise ask the user.
- **None listed** — query Notion for Active initiatives using `mcp__claude_ai_Notion__notion-search`, ask the user to pick.
