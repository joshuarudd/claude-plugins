# Initiative Resolution

Shared pattern used by all OST skills that operate on a specific initiative.

**Prerequisite:** Run backend resolution first. See `skills/setup/backend-resolution.md`.

## Steps

### 1. Resolve Backend

Follow `skills/setup/backend-resolution.md` to determine the backend and load the correct reference file.

### 2. Resolve the Target Initiative

Check the project's CLAUDE.md for an `## OST Initiatives` section.

#### Initiative format

Each initiative is a list item. The value is interpreted differently per backend:

- **Markdown backend** — the value is the **folder path** (relative to the vault/workspace root) containing the `OST/` subfolder. A display label is derived by replacing `/` with ` / `.
- **Notion backend** — the value is the **initiative name** in the Notion database.

```markdown
## OST Initiatives
- Aiwyn/Billing
- Aiwyn/Design System
- Collabrios/Color System
```

In the example above (markdown backend): `Aiwyn/Billing` is the folder path, and the display label is `Aiwyn / Billing`.

#### Selection logic

- **One initiative listed** — use it automatically.
- **Multiple listed** — if `$ARGUMENTS` matches (or is a substring of) a listed value, use it; otherwise show the list and ask the user.
- **None listed** — handle by backend (see below).

### 3. Backend-Specific Resolution

#### Markdown backend

The selected initiative value is the folder path where the OST lives.

1. The OST folder is at `{initiative-path}/OST/`.
2. Verify the folder exists. If not, tell the user to run `/ost:setup` to create the folder structure.

If no initiatives are listed, check for a standalone `base-path` in `## OST Configuration` as a fallback (legacy single-initiative format).

#### Notion backend

Read data source IDs from the project's CLAUDE.md `## OST Notion Data Sources` section. If missing, tell the user to run `/ost:setup` first.

Read `skills/setup/notion-backend.md` from this plugin for Notion tool names and property mappings.

If no initiative is listed in `## OST Initiatives`:
- Query Notion for Active initiatives using `mcp__claude_ai_Notion__notion-search`.
- Ask the user to pick one.

Resolve the initiative's Notion page URL for use in relation properties.
