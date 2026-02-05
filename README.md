# claude-plugins

A plugin marketplace for Claude Code. Contains skills and slash commands that extend Claude Code's capabilities.

## What is this?

This is a Claude Code plugin marketplace — a collection of plugins that can be installed into Claude Code to add new skills and slash commands. Plugins are markdown-based instructions that Claude follows when you invoke them via `/command-name` or that Claude auto-invokes when contextually relevant.

## Installation

Add this marketplace to Claude Code:

```
/plugin marketplace add joshuarudd/claude-plugins
```

Then install any available plugin:

```
/plugin install plugin-name@claude-plugins
```

## Available Plugins

| Plugin | Description |
|--------|-------------|
| [core-skills](plugins/core-skills/) | General improvements to Claude's behavior — date lookups, and more to come. |

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to add your own.

## Creating a Plugin

The quickest way to create a new plugin:

1. Copy an existing plugin as a starting point (e.g., `core-skills`):
   ```bash
   cp -r plugins/core-skills plugins/your-plugin-name
   ```

2. Edit the plugin manifest at `plugins/your-plugin-name/.claude-plugin/plugin.json` — update the `name`, `description`, and `author` fields.

3. Replace the skills under `plugins/your-plugin-name/skills/` with your own skill directories, each containing a `SKILL.md`.

4. Register the plugin in `.claude-plugin/marketplace.json` by adding an entry to the `plugins` array:
   ```json
   {
     "name": "your-plugin-name",
     "source": "./plugins/your-plugin-name",
     "description": "What your plugin does"
   }
   ```

5. Test locally:
   ```bash
   claude --plugin-dir ./plugins/your-plugin-name
   ```

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full guide.

## Plugin Structure

Each plugin follows this structure:

```
plugins/your-plugin-name/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest (required)
├── skills/
│   └── skill-name/
│       └── SKILL.md          # Skill definition (required)
└── README.md                 # Plugin documentation (recommended)
```

Skills are invoked as `/plugin-name:skill-name` in Claude Code.

## License

MIT
