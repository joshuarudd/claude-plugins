---
name: list-opportunities
description: List the current Opportunity Solution Tree for an initiative, showing the full hierarchy of Outcomes, Opportunities, Solutions, and Experiments with their status and confidence. Use when the user wants to see their current tree, review opportunities, or check the state of their OST.
argument-hint: <initiative name (optional)>
---

# List Opportunities

Query Notion and display the Opportunity Solution Tree as a hierarchical text tree.

## Workflow

1. Resolve which Initiative to display (see Initiative Resolution below).
2. Query all OST Nodes for that Initiative.
3. Build and display the tree hierarchy.

## Initiative Resolution

Check the current project's CLAUDE.md for an `## OST Initiatives` section:

- **One initiative listed** — use it automatically.
- **Multiple listed** — if `$ARGUMENTS` matches one, use it; otherwise ask.
- **None listed** — query Notion for Active initiatives, ask the user to pick.

If `$ARGUMENTS` contains an initiative name, use that directly.

## Steps

1. Read data source IDs from the project's CLAUDE.md `## OST Notion Data Sources` section. Read `backends/notion.md` from this plugin for Notion tool names and property mappings. If the data sources section is missing, tell the user to run `/ost:setup` first.
2. Search for the Initiative page URL using `mcp__claude_ai_Notion__notion-search`.
3. Search for all OST Nodes linked to that Initiative using `mcp__claude_ai_Notion__notion-search`.
4. Fetch each node's details to get: Name, Type, Status, Confidence, Evidence Summary, and Parent relation.
5. Build the tree by following Parent relations:
   - Nodes with no Parent are roots (typically Outcomes).
   - Each node's children are nodes whose Parent points to it.
6. Display the tree using this format:

```
## <Initiative Name> — Opportunity Solution Tree

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

## Display Rules

- Sort nodes within each level alphabetically by name.
- Show Status and Confidence for every node.
- Show Evidence Summary if present (truncate to ~80 chars if very long).
- If a node has no children, don't show any branch indicator below it.
- If the tree is empty (no nodes found), tell the user and suggest using `/ost:capture-feedback` or `/ost:process-transcript` to start building the tree.
