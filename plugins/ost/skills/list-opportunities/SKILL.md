---
name: list-opportunities
description: Use when the user wants to see their current Opportunity Solution Tree, review the hierarchy, or check the state of their OST.
---

# List Opportunities

Query Notion and display the Opportunity Solution Tree as a hierarchical text tree.

## Workflow

1. Resolve Initiative and load Notion config. See `skills/setup/initiative-resolution.md`.
2. Search for all OST Nodes linked to the Initiative.
3. Fetch each node's details: Name, Type, Status, Confidence, Evidence Summary, Parent relation.
4. Build the tree by following Parent relations (no Parent = root node).
5. Display using the format below.

## Output Format

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

- Sort alphabetically within each level.
- Show Status and Confidence for every node.
- Show Evidence Summary if present (truncate to ~80 chars).
- No branch indicator below childless nodes.
- If tree is empty, suggest `/ost:capture-feedback` or `/ost:process-transcript` to start building it.
