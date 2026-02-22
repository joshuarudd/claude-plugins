# Markdown Backend Reference

This file contains all markdown-specific implementation details for the OST plugin. Skills reference this file for file operations, frontmatter schema, and folder conventions.

## Configuration

The markdown backend is configured in the project's CLAUDE.md:

```markdown
## OST Configuration
- backend: markdown
- obsidian-features: true

## OST Initiatives
- Aiwyn/Billing
- Aiwyn/Design System
- Collabrios/Color System
```

- **`obsidian-features`** — enables Obsidian CLI search, Base file generation, and Canvas generation. Set to `false` for plain markdown.

### Initiative format

Each initiative is listed as `- path` where path is the folder containing the `OST/` subfolder (relative to the vault or workspace root). A display label is derived by replacing `/` with ` / ` (e.g., `Aiwyn/Billing` → `Aiwyn / Billing`).

One initiative = auto-selected. Multiple = prompted or matched from `$ARGUMENTS`. See `skills/setup/initiative-resolution.md` for full selection logic.

## Folder Structure

The OST lives inside the configured base path as an `OST/` subfolder:

```
{initiative-path}/
  OST/
    Outcomes/
      Increase billing adoption.md
    Opportunities/
      Users confused by invoice layout.md
    Solutions/
      Redesign invoice template.md
    Experiments/
      A-B test new invoice layout.md
    Assumptions/
      Firms prefer email invoices.md
    OST.canvas               (generated, obsidian-features only)
    OST.base                 (generated, obsidian-features only)
```

### Node type subfolders

| Subfolder | OST Type | Canvas Color |
|-----------|----------|--------------|
| `Outcomes/` | Outcome | Cyan (`"5"`) |
| `Opportunities/` | Opportunity | Yellow (`"3"`) |
| `Solutions/` | Solution | Green (`"4"`) |
| `Experiments/` | Experiment | Purple (`"6"`) |
| `Assumptions/` | Assumption | Orange (`"2"`) |

## Frontmatter Schema

Every OST node file uses this frontmatter:

```yaml
---
ost-type: Opportunity
status: Active
confidence: 50
parent: "[[Increase billing adoption]]"
evidence-summary: "3 users mentioned confusion with invoice layout"
source-interviews:
  - "[[P1 - Controller at Smith & Co - 2026-01-15]]"
  - "[[P2 - Office Manager - 2026-02-03]]"
created-date: 2026-02-22
---
```

### Field reference

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `ost-type` | string | Yes | `Outcome`, `Opportunity`, `Solution`, `Experiment`, or `Assumption` |
| `status` | string | Yes | `Active`, `Validated`, `Invalidated`, or `On Hold` |
| `confidence` | number | Yes | 0–100 |
| `parent` | wikilink | No | Empty for Outcomes (root nodes). Wikilink to parent node. |
| `evidence-summary` | string | No | One-liner for quick scanning |
| `source-interviews` | list of wikilinks | No | Links to transcript/interview files |
| `created-date` | date | Yes | ISO 8601 date |

### Outcome nodes

Outcomes are root nodes — they have no `parent`. Example:

```yaml
---
ost-type: Outcome
status: Active
confidence: 50
evidence-summary: "Primary business objective for Q1"
created-date: 2026-02-22
---
```

## Operations

### Find OST Location

1. Resolve the target initiative using `skills/setup/initiative-resolution.md`. The resolved initiative value is the folder path.
2. The OST folder is at `{initiative-path}/OST/`.
3. Verify the folder exists. If not, create it along with the type subfolders.

### Search for Existing Node

Search for a node by name or semantic match:

1. Glob `{initiative-path}/OST/{type}/*.md` (or `{initiative-path}/OST/**/*.md` to search all types).
2. Read frontmatter from each matching file.
3. Use semantic matching (Claude judges whether the node describes the same user problem or concept, even if worded differently).

Example search pattern:
```
Glob: {initiative-path}/OST/Opportunities/*.md
```

Then read each file's frontmatter and title to find semantic matches.

### Create Node

Write a new `.md` file in the appropriate type subfolder:

1. Determine the subfolder from the node's `ost-type`.
2. Use the node name as the filename (e.g., `Users confused by invoice layout.md`).
3. Write the file with frontmatter and body content.

Example:
```markdown
---
ost-type: Opportunity
status: Active
confidence: 50
parent: "[[Increase billing adoption]]"
evidence-summary: "3 users mentioned confusion with invoice layout"
source-interviews:
  - "[[P1 - Controller at Smith & Co - 2026-01-15]]"
created-date: 2026-02-22
---

## Evidence

- P1: "I couldn't figure out which charges were recurring vs one-time"
- P2: "The invoice layout doesn't match our accounting categories"
```

### Update Node

1. Read the existing file.
2. Edit frontmatter fields (e.g., update `confidence`, append to `source-interviews`).
3. Append to or modify the body content (e.g., add new evidence quotes).

### Read Node

Read the file directly. Parse YAML frontmatter (between `---` delimiters) and markdown body separately.

### List Tree

1. Glob all `{initiative-path}/OST/**/*.md` files.
2. Read each file's frontmatter: `ost-type`, `status`, `confidence`, `parent`, `evidence-summary`.
3. Build the tree by following `parent` wikilinks (extract the note name from `[[Note Name]]`).
4. Outcomes (no parent) are root nodes.

### Delete Node

Delete the `.md` file from its subfolder. Check for any other nodes that reference it as `parent` and warn before proceeding.

## Obsidian-Specific Enhancements

These features are only available when `obsidian-features: true` in the OST configuration.

### Search with Obsidian CLI

When available, use the Obsidian CLI (`Obsidian` is capitalized in PATH) for faster search instead of Glob + Read:

```
Obsidian search query=<text>
```

This uses Obsidian's index and understands aliases, tags, and frontmatter. Falls back to Glob + Grep when Obsidian CLI is not available.

### Canvas Generation (`OST.canvas`)

Generate an Obsidian Canvas file for interactive tree visualization. The canvas uses `file` type nodes that reference actual `.md` files — clicking a node in Obsidian opens the note.

Canvas JSON structure:

Node and edge IDs must be **16-character lowercase hex strings** (e.g., `"6f0ad84f44ce9c17"`). Generate unique random hex IDs for each node and edge.

```json
{
  "nodes": [
    {
      "id": "6f0ad84f44ce9c17",
      "type": "file",
      "file": "Aiwyn/Billing/OST/Outcomes/Increase billing adoption.md",
      "x": 0,
      "y": 0,
      "width": 300,
      "height": 60,
      "color": "5"
    }
  ],
  "edges": [
    {
      "id": "a3b2c1d0e9f8a7b6",
      "fromNode": "6f0ad84f44ce9c17",
      "toNode": "1a2b3c4d5e6f7a8b",
      "fromSide": "bottom",
      "toSide": "top"
    }
  ]
}
```

Layout rules:
- **Top-down tree**: Outcomes at top, Opportunities below, Solutions below that, etc.
- **Colors**: By node type (see Node type subfolders table).
- **Edges**: Follow `parent` relationships, flowing top to bottom.
- **Spacing**: 350px horizontal between siblings, 150px vertical between levels.

Write to `{initiative-path}/OST/OST.canvas`.

### Base File Generation (`OST.base`)

Generate an Obsidian Base file for tabular database view. Scoped to the local OST folder.

```yaml
filters:
  and:
    - file.hasProperty("ost-type")
    - 'file.ext == "md"'

formulas:
  confidence_bar: 'if(confidence, "▓".repeat((confidence / 10).floor()) + "░".repeat(10 - (confidence / 10).floor()), "")'
  parent_name: 'if(parent, parent, "—")'

properties:
  formula.confidence_bar:
    displayName: "Confidence"
  formula.parent_name:
    displayName: "Parent"

views:
  - type: table
    name: "All Nodes"
    order:
      - file.name
      - ost-type
      - status
      - formula.confidence_bar
      - formula.parent_name
      - evidence-summary
    groupBy:
      property: ost-type
      direction: ASC

  - type: table
    name: "Needs Evidence"
    filters:
      and:
        - 'status == "Active"'
        - 'confidence < 50'
    order:
      - file.name
      - ost-type
      - formula.confidence_bar
      - evidence-summary
```

Write to `{initiative-path}/OST/OST.base`.

## Interviews and Source Documents

Interviews and transcripts are **not** managed by the OST plugin. They live wherever the user's vault structure dictates (e.g., `Library/Transcripts/`). OST nodes reference them via `source-interviews` wikilinks.

Two supported patterns:
1. **External** (recommended): transcripts in a shared location. OST nodes link to them via wikilinks.
2. **Self-contained**: users create an `Interviews/` subfolder inside `OST/` for colocation.

## Data Model Comparison with Notion Backend

| Concept | Notion backend | Markdown backend |
|---------|---------------|-----------------|
| Initiative | Database entry, top-level container | The product/topic folder containing `OST/` |
| OST Node | Row in OST Nodes database | File in `OST/{type}/` subfolder |
| Tree hierarchy | Parent self-relation in database | `parent` wikilink in frontmatter |
| Interview link | Relation to Interviews database | `source-interviews` wikilinks |
| Node identity | Notion page ID | Filename (note title) |
