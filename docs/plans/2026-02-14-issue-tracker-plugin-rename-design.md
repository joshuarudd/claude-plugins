# Issue Tracker Plugin Rename — Design

## Context

The `product-development` plugin has one skill (`capture-issue`) for capturing GitHub issues from feedback during development sessions. The name "product-development" is too broad — it overlaps conceptually with both the OST plugin (strategic discovery) and superpowers (development process). The plugin needs a focused identity and its skill needs to follow writing-skills conventions.

## Layering

- **OST** = strategic discovery (outcomes, opportunities, gaps — "what should we build?")
- **issue-tracker** = tactical execution (specific issues, bugs, stories — "what needs to get done?")
- **superpowers** = development process (TDD, debugging, planning — "how do we build it?")

## Changes

### Plugin rename: `product-development` → `issue-tracker`

- Rename directory `plugins/product-development/` → `plugins/issue-tracker/`
- Update `plugin.json`: name to `issue-tracker`, description to "Capture and track actionable issues during development sessions."
- Update `marketplace.json`: plugin entry name, source path, and description

### Skill rename: `capture-issue` → `capturing-issues`

- Rename directory `skills/capture-issue/` → `skills/capturing-issues/`
- Update SKILL.md frontmatter: name to `capturing-issues`
- Rewrite description to triggering-conditions only: "Use when the user provides feedback, enhancement ideas, bugs, or observations during a development session and wants them captured as issues without disrupting the current working context."

### Command file

- Keep filename as `capture-issue.md` (commands use imperative form)
- Update internal reference to new skill path

## Out of scope

- OST plugin skill renaming to gerunds (separate effort)
- Adding new skills to the plugin
- SKILL.md body content changes (beyond frontmatter)
