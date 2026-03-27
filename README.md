# B2X Utilities

Plugin lifecycle utilities for [B2X Marketing](https://github.com/B2X-Marketing). Package any project repo as a Claude Code plugin and push updates to the b2x-local marketplace.

## Skills

- **make-a-plugin** — Convert a B2X project repository into a Claude Code plugin with skills, register it in the local marketplace, and install it
- **push-plugin-update** — Bump version, clear cache, and reinstall a plugin so development changes take effect

## Installation

```bash
claude plugin install b2x-utilities@b2x-local
```

## Usage

From any B2X project directory:

```
# Turn the current repo into a plugin
> make this a plugin

# After making changes to a plugin's source
> push plugin update
```

## Prerequisites

- [Claude Code](https://claude.ai/code) CLI
- b2x-local marketplace registered in `~/.claude/plugins/known_marketplaces.json`
