# B2X Utilities

Plugin lifecycle utilities for B2X Marketing. Two skills for managing the b2x-local Claude Code plugin marketplace.

## Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `make-a-plugin` | "make this a plugin", "package as plugin" | Convert a repo into a registered plugin |
| `push-plugin-update` | "push plugin update", "bump plugin version" | Deploy changes from source to runtime |
| `export-to-apple-notes` | "save to Apple Notes", "post to Notes", "export to Notes" | Format and post content to Apple Notes via MCP |

## b2x-local Marketplace Architecture

All B2X plugins are registered in a local marketplace at:

```
~/.claude/plugins/marketplaces/b2x-local/
├── .claude-plugin/
│   └── marketplace.json          # Lists all plugins
└── plugins/
    ├── b2x-marketing-toolkit/    # Symlink → source repo
    ├── b2x-utilities/            # Symlink → source repo
    └── ...
```

### Key Paths

- **Marketplace JSON**: `~/.claude/plugins/marketplaces/b2x-local/.claude-plugin/marketplace.json`
- **Known Marketplaces**: `~/.claude/plugins/known_marketplaces.json`
- **Installed Plugins**: `~/.claude/plugins/installed_plugins.json`
- **Plugin Cache**: `~/.claude/plugins/cache/b2x-local/{plugin-name}/{version}/`

### How It Works

1. Each plugin source repo has `.claude-plugin/plugin.json` and a `skills/` directory
2. A symlink in `b2x-local/plugins/` points to the source repo
3. `claude plugin install {name}@b2x-local` copies the source into the cache
4. Claude Code loads skills from the cache at session start

### Update Flow

Source repo change → bump version in `plugin.json` → clear cache → reinstall → restart session

## Conventions

- SKILL.md body: under ~2,100 characters
- Writing style: imperative form, no second person, active voice
- Plugin names: kebab-case, prefixed with `b2x-`
- Skill names: kebab-case, match directory name
- Every skill must have `references/WORKFLOW.md`
