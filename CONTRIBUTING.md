# Contributing a Plugin

## Quick Start

1. Copy an existing plugin as a starting point (e.g., `core-skills`):
   ```bash
   cp -r plugins/core-skills plugins/your-plugin-name
   ```

2. Update `plugins/your-plugin-name/.claude-plugin/plugin.json`:
   ```json
   {
     "name": "your-plugin-name",
     "description": "Brief description of what your plugin does",
     "version": "1.0.0",
     "author": {
       "name": "Your Name"
     }
   }
   ```

3. Create your skill(s) under `plugins/your-plugin-name/skills/`. Each skill needs its own directory with a `SKILL.md` file.

4. Register in `.claude-plugin/marketplace.json` by adding to the `plugins` array:
   ```json
   {
     "name": "your-plugin-name",
     "source": "./plugins/your-plugin-name",
     "description": "Brief description of what your plugin does"
   }
   ```

5. Test:
   ```bash
   claude --plugin-dir ./plugins/your-plugin-name
   ```

## Naming Rules

- Plugin names must be **kebab-case** (lowercase letters, numbers, hyphens)
- No spaces, underscores, or special characters
- Names must be unique within this marketplace
- Do not use names that start with `_` (reserved for templates and internal use)

## Writing a SKILL.md

Every skill needs a `SKILL.md` file with YAML frontmatter and markdown instructions:

```markdown
---
description: What this skill does and when to use it
---

Your instructions for Claude go here.
```

### Common Frontmatter Fields

| Field | Description |
|-------|-------------|
| `name` | Display name (defaults to directory name if omitted) |
| `description` | What the skill does — Claude uses this to decide when to load it |
| `disable-model-invocation` | Set `true` to prevent Claude from auto-invoking (user-only) |
| `user-invocable` | Set `false` to hide from the `/` menu (Claude-only) |
| `allowed-tools` | Restrict which tools Claude can use (e.g., `Read, Grep, Glob`) |
| `context` | Set to `fork` to run in a subagent |

### Using Arguments

Use `$ARGUMENTS` in your skill content to capture user input:

```markdown
---
description: Greet someone
---

Say hello to $ARGUMENTS warmly.
```

Invoke: `/your-plugin:greet Alice`

## Plugin Directory Layout

```
plugins/your-plugin-name/
├── .claude-plugin/
│   └── plugin.json           # Required: plugin metadata
├── skills/
│   └── skill-name/
│       ├── SKILL.md          # Required: skill definition
│       ├── reference.md      # Optional: detailed docs
│       └── scripts/          # Optional: helper scripts
│           └── helper.sh
└── README.md                 # Recommended: plugin documentation
```

### Important

- The `.claude-plugin/` directory must contain ONLY `plugin.json`
- Skills, commands, agents, and hooks go at the plugin root level, NOT inside `.claude-plugin/`
- All paths in plugin.json must be relative and start with `./`

## Testing

Test your plugin locally before committing:

```bash
# Load plugin directly
claude --plugin-dir ./plugins/your-plugin-name

# Then try your skill
/your-plugin-name:skill-name
```

## Checklist

Before submitting a new plugin:

- [ ] Plugin name is kebab-case and unique
- [ ] `plugin.json` has name, description, and version
- [ ] At least one skill with a valid `SKILL.md`
- [ ] SKILL.md has a `description` in frontmatter
- [ ] Plugin is registered in `marketplace.json`
- [ ] Tested with `claude --plugin-dir`
- [ ] README.md describes what the plugin does
