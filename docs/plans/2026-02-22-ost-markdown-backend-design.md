# OST Markdown Backend + Vault Reorganization Design

**Date**: 2026-02-22
**Status**: Draft

## Context

The OST (Opportunity Solution Tree) plugin currently uses Notion as its data layer. This design describes a markdown-based backend that uses local files as the source of truth, with optional Obsidian-specific enhancements (CLI, Bases, Canvas). It also addresses a broader vault reorganization to support active projects and discovery initiatives.

## Part 1: Vault Organization

### Problem

The vault has two organizational forces in tension:

- **By nature** — Library, PKM, Journal, Personal (what kind of thing it is)
- **By context** — Aiwyn, All Saints, Job Search 2026 (what it's for)

The "by nature" folders work well and stay unchanged. The "by context" folders need a formalized structure to support active projects, long-lived products, and OST discovery initiatives — with clear lifecycle management.

### Design: Areas with Topics

**Area** = an organizational context (employer, client, church, standalone endeavor). Area folders live at the vault root, as they already do.

**Topic** = a natural grouping of related work inside an area. Topics come in three flavors, distinguished by frontmatter — not folder structure:

| Flavor | Description | Examples |
|--------|-------------|---------|
| Product | Ongoing thing you're responsible for | Billing, Design System, Music Ministry |
| Project | Time-bound, completable work | Invoice Redesign, Brand Refresh 2024 |
| General | Loose docs that don't fit a product or project | Team notes, onboarding docs |

### Folder structure

```
Aiwyn/                          ← area (employer)
  Billing/                      ← product topic
    OST/                        ← discovery tree for Billing
      Outcomes/
      Opportunities/
      Solutions/
    Invoice Redesign/           ← project (born from OST)
  Design System/                ← product topic
    OST/                        ← separate discovery tree
  Teams/                        ← general topic
  Archive/                      ← completed topics

Collabrios/                     ← area (client engagement)
  Color System/                 ← product topic
    OST/
  Archive/

All Saints/                     ← area (church)
  Music Ministry/               ← ongoing topic
  Building Committee/           ← ongoing topic
  Archive/

Job Search 2026/                ← standalone area (no org)

Side Projects/                  ← personal builds

Archives/                       ← completed areas (entire area inactive)
  UserVoice/
  RootedGood/

Bases/                          ← Obsidian database views (unchanged)
Journal/                        ← daily notes only (unchanged)
Library/                        ← reference material (unchanged)
Personal/                       ← personal life (unchanged)
PKM/                            ← original thinking (unchanged)
```

### Key decisions

- **No area index note.** The folder is the area. Org-level info lives on the canonical org note in `Library/Organizations/`. No duplication.
- **Topic index notes** inside each topic folder carry frontmatter: `type`, `area` (wikilink), `status`, `created-date`.
- **OST lives at the product/topic level**, not the area level. A product like Billing owns its discovery tree. Multiple products within an area each get their own OST.
- **Projects emerge from the tree.** When an OST Solution gets validated, a project folder is created alongside the OST, with wikilinks connecting them.

### Lifecycle

- **Completed topic** → move to `{Area}/Archive/`
- **Entire area becomes inactive** → move the whole area folder to `Archives/`
- **Discoverability** → a Base file (e.g., `Bases/Active Projects.base`) filters by `status: active` across all areas

### Topic index note frontmatter

```yaml
---
type: product                   # product | project | topic
area: "[[Aiwyn]]"
status: active                  # active | done | on-hold
created-date: 2026-01-15
---
```

---

## Part 2: OST Folder Structure

An OST lives inside a product or topic folder as a subfolder called `OST/`.

### Structure

```
Billing/
  OST/
    Outcomes/
      Increase billing adoption.md
      Reduce billing errors.md
    Opportunities/
      Users confused by invoice layout.md
      Firms forget to generate invoices.md
    Solutions/
      Redesign invoice template.md
      Auto-generate reminders.md
    Experiments/
      A-B test new invoice layout.md
    Assumptions/
      Firms prefer email invoices.md
    OST.canvas                  ← generated interactive tree view
    OST.base                    ← local database view
```

### Node types as subfolders

| Subfolder | OST Role | Canvas Color |
|-----------|----------|--------------|
| Outcomes/ | Root goals — measurable product/business objectives | Cyan (`"5"`) |
| Opportunities/ | User problems discovered through research | Yellow (`"3"`) |
| Solutions/ | Proposed ways to address opportunities | Green (`"4"`) |
| Experiments/ | Tests to validate solutions or assumptions | Purple (`"6"`) |
| Assumptions/ | Beliefs that need validation | Orange (`"2"`) |

### Node frontmatter schema

```yaml
---
ost-type: Opportunity           # Outcome | Opportunity | Solution | Experiment | Assumption
status: Active                  # Active | Validated | Invalidated | On Hold
confidence: 50                  # 0-100
parent: "[[Increase billing adoption]]"
evidence-summary: "3 users mentioned confusion with invoice layout"
source-interviews:
  - "[[P1 - Controller at Smith & Co - 2026-01-15]]"
  - "[[P2 - Office Manager - 2026-02-03]]"
created-date: 2026-02-22
---
```

- `parent` is a wikilink. Outcomes have no parent (tree roots).
- The subfolder implies `ost-type`, but frontmatter makes it explicit for queries.
- No "initiative" property — the OST's location in the folder hierarchy identifies which product it belongs to.

### No Interviews subfolder

Transcripts and interview records are **external evidence sources**, not OST-managed artifacts. They live wherever the user's vault structure dictates (e.g., `Library/Transcripts/`). OST nodes reference them via `source-interviews` wikilinks. Obsidian backlinks automatically show every node that cites a given transcript.

The plugin docs describe two patterns:
1. **External** (recommended): transcripts live in a shared location like `Library/Transcripts/`. OST nodes link to them.
2. **Self-contained**: users who prefer colocation can create an `Interviews/` subfolder inside `OST/`.

### How projects emerge from the tree

When a Solution node is validated and becomes real work:

```
Billing/
  OST/
    Solutions/
      Redesign invoice template.md    ← validated, links to project
  Invoice Redesign/                   ← project folder
    requirements.md
```

The project's index note links back to the Solution node. The Solution node links forward to the project. Both directions via wikilinks.

---

## Part 3: Backend Abstraction

The plugin supports two backends: Notion and markdown. Each project declares its backend in CLAUDE.md.

### Configuration

**Markdown backend:**

```markdown
## OST Configuration
- backend: markdown
- obsidian-features: true       # enables CLI search, Bases, Canvas
```

**Notion backend:**

```markdown
## OST Configuration
- backend: notion

## OST Notion Data Sources
- Initiatives: <data-source-id>
- OST Nodes: <data-source-id>
- Interviews: <data-source-id>
```

### Backend reference files

The plugin ships two reference files:

| File | Purpose |
|------|---------|
| `notion-backend.md` | Tool names, property mappings, data source patterns for Notion |
| `markdown-backend.md` | File operations, frontmatter schema, folder conventions for markdown |

Each skill reads the appropriate reference file based on the configured backend. Skill workflows remain identical — only the operations differ.

### Operation mapping

| Operation | Notion backend | Markdown backend |
|-----------|---------------|-----------------|
| Find OST location | Query data source IDs from config | Read `base-path` from CLAUDE.md, find `OST/` subfolder |
| Search for existing node | `notion-search` + `notion-fetch` | Glob `OST/{type}/*.md` + Read frontmatter + semantic match |
| Create node | `notion-create-pages` with properties | Write new `.md` file with frontmatter in type subfolder |
| Update node | `notion-update-page` | Read + Edit frontmatter and body |
| Read node | `notion-fetch` | Read the file |
| List tree | Query all nodes, follow relations | Glob all `OST/**/*.md`, read `parent` frontmatter, build tree |
| Visualize | Text-based tree output | Canvas + Base + optional mermaid |

### Obsidian-specific enhancements

When `obsidian-features: true`:
- **Search** uses `Obsidian search` instead of Glob+Grep
- **Base file** (`OST.base`) gets generated alongside the tree
- **Canvas file** (`OST.canvas`) gets generated for interactive visualization

When `obsidian-features: false` (plain markdown):
- Search falls back to Glob + Grep
- No `.base` or `.canvas` generation
- Fully functional, just less visual

### Data model difference between backends

| Concept | Notion backend | Markdown backend |
|---------|---------------|-----------------|
| Initiative | Database entry, top-level container | The product/topic folder containing `OST/` |
| OST Node | Row in OST Nodes database | File in `OST/{type}/` subfolder |
| Tree hierarchy | Parent self-relation in database | `parent` wikilink in frontmatter |
| Interview link | Relation to Interviews database | `source-interviews` wikilinks |

---

## Part 4: Plugin Skills

### Skill roster

| Skill | Purpose | Input |
|-------|---------|-------|
| `setup` | Configure backend, verify access, set OST path | — |
| `set-outcomes` | Define root Outcome nodes | Outcome descriptions |
| `capture-feedback` | File a specific insight into the tree | Quote, observation, or note |
| `analyze-transcript` | Systematically analyze a source document for all opportunities | Path to transcript or meeting notes |
| `list-opportunities` | Render the tree + regenerate canvas/base | — |

### Key changes from current plugin

- **`process-transcript` renamed to `analyze-transcript`** — reflects that it analyzes existing transcripts rather than filing them
- **`analyze-transcript` does not create transcript files** — the source document already exists; the skill reads it, extracts opportunities, creates/updates OST nodes, and links back to the source
- **`capture-feedback` handles targeted input** — a specific quote, observation, or insight filed to the tree
- **Skills are backend-agnostic** — same workflow regardless of Notion or markdown, just different operations

### Skill workflows (markdown backend)

**`setup`:**
1. Check if CLAUDE.md has `## OST Configuration`
2. If missing, walk user through: choose backend, set base path, enable Obsidian features
3. Verify folder structure exists (or create it)

**`set-outcomes`:**
1. Read base path from config
2. Glob `OST/Outcomes/*.md` for existing outcomes
3. Present current state, ask user for additions/modifications
4. Write new `.md` files in `Outcomes/` with frontmatter

**`capture-feedback`:**
1. Read base path from config
2. Parse feedback into discrete opportunities
3. For each: search existing nodes (Glob + Read frontmatter + semantic match by Claude)
4. Match found → append evidence to existing node
5. No match → create new Opportunity node with frontmatter and evidence
6. Report what was created/updated

**`analyze-transcript`:**
1. Read the source document (transcript, meeting notes)
2. Systematically extract: pain points, workarounds, unmet needs, confusion, feature requests (reframed as needs)
3. For each extracted opportunity: search existing nodes for semantic match
4. Match found → append evidence, add wikilink to source in `source-interviews`
5. No match → create new Opportunity node with source linked
6. Report summary: each opportunity (new vs updated) with key evidence

**`list-opportunities`:**
1. Glob all `OST/**/*.md` files
2. Read each file's frontmatter: `ost-type`, `status`, `confidence`, `parent`, `evidence-summary`
3. Build tree by following `parent` wikilinks
4. Display as formatted text tree
5. If `obsidian-features: true`: regenerate `OST.canvas` and `OST.base`

---

## Part 5: Visualization

Three layers serving different purposes. Canvas and Base are generated only when `obsidian-features: true`.

### Canvas (`OST.canvas`) — primary interactive view

Generated read-only visualization. Uses `file` type nodes referencing actual `.md` files — clicking a node in Obsidian opens the note.

- **Layout**: top-down tree. Outcomes at top, Opportunities below, Solutions below that, etc.
- **Colors**: by node type (see table in Part 2)
- **Edges**: follow `parent` relationships, flowing top to bottom
- **Regenerated** each time `list-opportunities` runs. Source of truth is always the markdown files.

Canvas edits do NOT flow back to markdown files. Bidirectional sync is a potential future enhancement.

### Base file (`OST.base`) — tabular database view

Scoped to the local OST folder. Provides filtering, sorting, grouping.

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

### Mermaid — embedded quick view

Lightweight inline visualization generated as a code block. Not interactive, but renders anywhere — useful for embedding in daily notes or topic index notes.

```
graph TD
    A["🎯 Increase billing adoption"] --> B["💡 Users confused by layout"]
    A --> C["💡 Firms forget to generate"]
    B --> D["🔧 Redesign invoice template"]
    style A fill:#e0f7fa
    style B fill:#fff9c4
    style D fill:#c8e6c9
```

---

## Part 6: Implementation Plan

### Files to add

| File | Purpose |
|------|---------|
| `skills/setup/markdown-backend.md` | Markdown backend reference (counterpart to `notion-backend.md`) |
| `skills/setup/backend-resolution.md` | Shared pattern: read backend config from CLAUDE.md, load correct reference file |
| `skills/analyze-transcript/SKILL.md` | Replaces `process-transcript` |

### Files to modify

| File | Change |
|------|--------|
| `skills/setup/SKILL.md` | Add backend choice (notion vs markdown), handle markdown folder creation |
| `skills/setup/initiative-resolution.md` | For markdown backend, "resolve initiative" becomes "find the OST folder at the configured base path" |
| `skills/capture-feedback/SKILL.md` | Make backend-agnostic: read backend config, branch on operations |
| `skills/set-outcomes/SKILL.md` | Make backend-agnostic |
| `skills/list-opportunities/SKILL.md` | Make backend-agnostic, add canvas/base generation for markdown backend |
| `.claude-plugin/plugin.json` | Update description to mention both backends, bump version |
| `README.md` | Document both backends, configuration, folder structure |

### Files to remove

| File | Reason |
|------|--------|
| `skills/process-transcript/SKILL.md` | Renamed to `analyze-transcript` |

### Implementation order

1. **Backend abstraction layer** — `backend-resolution.md` and `markdown-backend.md`. Foundation for everything else.
2. **Setup skill** — backend choice flow, markdown folder creation.
3. **Initiative resolution update** — support both backends.
4. **set-outcomes** — simplest skill, good first test of markdown backend.
5. **capture-feedback** — core workflow, validates search + create + update operations.
6. **analyze-transcript** — rename + update behavior (no file creation, just analysis and linking).
7. **list-opportunities** — tree rendering + canvas/base generation.
8. **README and plugin.json** — documentation and version bump.

---

## Open Questions

- **Active Projects Base**: The design mentions a `Bases/Active Projects.base` for discoverability across areas. Schema TBD — depends on broader vault reorganization timing.
- **Canvas bidirectional sync**: Deferred. Canvas is read-only for now. Could revisit with a reconciliation skill that diffs canvas edges against frontmatter `parent` relationships.
- **Notion backend parity**: The Notion backend's "Initiative" concept maps to "product/topic folder" in markdown. The existing Notion skills may need minor refactoring to align terminology, but can continue working as-is.
