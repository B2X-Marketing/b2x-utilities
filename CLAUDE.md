# B2X Marketplace — Utilities

Marketplace repo for utility plugins. Contributes plugins to the `b2x-local` Claude Code marketplace.

## Structure

```
b2x-marketplace-utilities/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace definition
└── plugins/
    └── b2x-utilities/            # Plugin: lifecycle tools + Apple Notes export
        ├── .claude-plugin/plugin.json
        └── skills/
            ├── make-a-plugin/
            ├── push-plugin-update/
            └── export-to-apple-notes/
```

## Plugins

### b2x-utilities

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `make-a-plugin` | "make this a plugin", "package as plugin" | Convert a repo into a registered plugin |
| `push-plugin-update` | "push plugin update", "bump plugin version" | Deploy changes from source to runtime |
| `export-to-apple-notes` | "save to Apple Notes", "post to Notes", "export to Notes" | Format and post content to Apple Notes via MCP |

## b2x-local Marketplace Architecture

Two source repos feed into a single runtime marketplace:

| Source repo | Plugins contributed |
|---|---|
| `b2x-marketplace-marketing` | strategy, search-intelligence |
| `b2x-marketplace-utilities` | b2x-utilities |

Runtime location: `~/.claude/plugins/marketplaces/b2x-local/`

Symlinks in `b2x-local/plugins/` point to each plugin's `plugins/<name>/` directory in the source repo.

### Key Paths

- **Marketplace JSON**: `~/.claude/plugins/marketplaces/b2x-local/.claude-plugin/marketplace.json`
- **Known Marketplaces**: `~/.claude/plugins/known_marketplaces.json`
- **Installed Plugins**: `~/.claude/plugins/installed_plugins.json`
- **Plugin Cache**: `~/.claude/plugins/cache/b2x-local/{plugin-name}/{version}/`

### Update Flow

Source repo change → bump version in `plugin.json` → clear cache → reinstall → restart session

## Conventions

- SKILL.md body: under ~2,100 characters
- Writing style: imperative form, no second person, active voice
- Plugin names: kebab-case, prefixed with `b2x-`
- Skill names: kebab-case, match directory name
- Every skill must have `references/WORKFLOW.md`
