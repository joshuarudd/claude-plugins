# Claude Code Plugin Marketplace

This is a personal Claude Code plugin marketplace repository.

## Structure

- `.claude-plugin/marketplace.json` — Marketplace registry. All plugins must be listed here.
- `plugins/<name>/` — Each plugin directory.
- `plugins/<name>/.claude-plugin/plugin.json` — Plugin manifest (required).
- `plugins/<name>/skills/<skill>/SKILL.md` — Skill definitions.

## Adding a Plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with at minimum a `name` field.
2. Add skills under `plugins/<name>/skills/<skill-name>/SKILL.md`.
3. Register the plugin in `.claude-plugin/marketplace.json` in the `plugins` array.
4. Test with `claude --plugin-dir ./plugins/<name>`.

## Conventions

- Plugin and skill names use kebab-case.
- All paths in configuration files are relative and start with `./`.
- Every SKILL.md must have a `description` field in its YAML frontmatter.
- Only `plugin.json` goes inside `.claude-plugin/`. Skills, commands, agents, and hooks go at the plugin root level.

## OST Initiatives
- OST Tool

## OST Notion Data Sources
- Initiatives: 304316e4-f7ff-8153-85fd-000b33f0ce5e
- OST Nodes: 304316e4-f7ff-811d-84f7-000b6d027167
- Interviews: 304316e4-f7ff-816c-b4ef-000b2699a61f
