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
