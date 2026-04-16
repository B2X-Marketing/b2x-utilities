# B2X Utilities

Plugin lifecycle utilities for Claude Code. Package any project repo as a plugin, publish locally or to a remote GitHub marketplace.

## Skills

- **make-a-plugin** — Convert a project repository into a Claude Code plugin with skills, register it in the local marketplace, and install it
- **local-publish-plugin** — Bump version, clear cache, and reinstall a plugin so development changes take effect locally
- **marketplace-publish-plugin** — Publish a plugin to a remote GitHub-hosted marketplace via commit or pull request
- **export-to-apple-notes** — Format and post content to Apple Notes via MCP
- **book-insights** — Generate 20 prioritized book insights and save to Notion Knowledge Base

## Installation

```bash
claude plugin install b2x-utilities@{marketplace}
```

## Usage

```
# Turn the current repo into a plugin
> make this a plugin

# After making changes to a plugin's source
> publish locally

# Publish to a GitHub marketplace
> publish to marketplace
```

## Prerequisites

- [Claude Code](https://claude.ai/code) CLI
- A local directory-type marketplace registered in `~/.claude/plugins/known_marketplaces.json`
