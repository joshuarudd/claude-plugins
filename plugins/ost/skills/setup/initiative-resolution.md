# Initiative Resolution

Shared pattern used by all OST skills that operate on a specific initiative.

**Prerequisite:** Run backend resolution first. See `skills/setup/backend-resolution.md`.

## Steps

### 1. Resolve Backend

Follow `skills/setup/backend-resolution.md` to determine the backend and load the correct reference file.

### 2. Resolve the Target Initiative

Check the project's CLAUDE.md for an `## OST Initiatives` section:

- **One initiative listed** — use it automatically.
- **Multiple listed** — if `$ARGUMENTS` names one, use it; otherwise ask the user.
- **None listed** — handle by backend (see below).

### 3. Backend-Specific Resolution

#### Markdown backend

The initiative maps to the configured `base-path`. Read `base-path` from the `## OST Configuration` section.

- Verify the `{base-path}/OST/` folder exists.
- If it doesn't exist, tell the user to run `/ost:setup` to create the folder structure.
- The initiative name (from `## OST Initiatives`) is used as a display label only — the tree location is determined by `base-path`.

#### Notion backend

Read data source IDs from the project's CLAUDE.md `## OST Notion Data Sources` section. If missing, tell the user to run `/ost:setup` first.

Read `skills/setup/notion-backend.md` from this plugin for Notion tool names and property mappings.

If no initiative is listed in `## OST Initiatives`:
- Query Notion for Active initiatives using `mcp__claude_ai_Notion__notion-search`.
- Ask the user to pick one.

Resolve the initiative's Notion page URL for use in relation properties.
