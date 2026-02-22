---
name: list-opportunities
description: Use when the user wants to see their current Opportunity Solution Tree, review the hierarchy, or check the state of their OST.
---

# List Opportunities

Query the configured backend and display the Opportunity Solution Tree as a hierarchical text tree.

## Workflow

1. Resolve initiative and load backend config. See `skills/setup/initiative-resolution.md`.
2. Retrieve all OST nodes.
3. Build the tree by following parent relationships.
4. Display using the format below.
5. If markdown backend with `obsidian-features: true`: regenerate Canvas and Base files.

## Steps

### 1. Retrieve All Nodes

#### Markdown backend

1. Read `base-path` from `## OST Configuration` in the project's CLAUDE.md.
2. Glob all `{base-path}/OST/**/*.md` files.
3. Read each file's frontmatter: `ost-type`, `status`, `confidence`, `parent`, `evidence-summary`.

#### Notion backend

1. Search for all OST Nodes linked to the resolved Initiative.
2. Fetch each node's details: Name, Type, Status, Confidence, Evidence Summary, Parent relation.

### 2. Build the Tree

- **Markdown**: follow `parent` wikilinks — extract the note name from `[[Note Name]]` and match to filenames.
- **Notion**: follow Parent relation properties.
- Nodes with no parent are root nodes (Outcomes).

### 3. Display

```
## {Initiative Name} — Opportunity Solution Tree

🎯 **Outcome Name** [Status] (Confidence%)
  Evidence: <evidence summary>
  └── 💡 **Opportunity Name** [Status] (Confidence%)
        Evidence: <evidence summary>
        ├── 🔧 **Solution Name** [Status] (Confidence%)
        │     Evidence: <evidence summary>
        │     └── 🧪 **Experiment Name** [Status] (Confidence%)
        │           Evidence: <evidence summary>
        └── 🔧 **Another Solution** [Status] (Confidence%)
              Evidence: <evidence summary>
```

### Display Rules

- Sort alphabetically within each level.
- Show Status and Confidence for every node.
- Show Evidence Summary if present (truncate to ~80 chars).
- No branch indicator below childless nodes.
- If tree is empty, suggest `/ost:capture-feedback` or `/ost:analyze-transcript` to start building it.

### Node type icons

| Type | Icon |
|------|------|
| Outcome | 🎯 |
| Opportunity | 💡 |
| Solution | 🔧 |
| Experiment | 🧪 |
| Assumption | 🤔 |

### 4. Regenerate Visualization (Markdown Backend Only)

Only when `obsidian-features: true` in the OST configuration. Reference `skills/setup/markdown-backend.md` for Canvas and Base file formats.

#### Canvas (`OST.canvas`)

Generate an Obsidian Canvas JSON file at `{base-path}/OST/OST.canvas`:

1. Create a `file` type node for each OST node, referencing the actual `.md` file path.
2. Assign colors by node type (see `markdown-backend.md` for color codes).
3. Layout top-down: Outcomes at y=0, each level 150px below, siblings 350px apart horizontally.
4. Create edges following `parent` relationships, from parent bottom to child top.

#### Base (`OST.base`)

Write the Base file at `{base-path}/OST/OST.base` using the template in `markdown-backend.md`. This provides tabular filtering and grouping in Obsidian.
