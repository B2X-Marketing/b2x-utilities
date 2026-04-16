---
name: local-publish-plugin
description: >
  Publish plugin changes to the local Claude Code runtime. This skill should
  be used when the user says "publish locally", "local publish", "refresh
  plugin", "update local plugin", "bump plugin version", "reinstall plugin",
  "deploy plugin changes", or has modified a plugin's source and needs the
  changes reflected in the local runtime.
version: 1.0.0
author: Luis Capobianco
tags:
  - plugin
  - publish
  - local
  - cache
---

# Local Publish Plugin

Publish changes from a plugin inside a marketplace repo to the local Claude Code runtime. This is a local-only operation: it bumps the version, clears the cache, and reinstalls from the symlinked source. It does not push to GitHub or any remote marketplace.

Read `references/WORKFLOW.md` for the detailed execution protocol.

## Inputs

- **Required:** Plugin name, or path to the marketplace repo (default: current working directory)
- **Optional:** Version bump type — `patch` (default), `minor`, or `major`; skip-bump flag for cache-only refresh

## Workflow

1. Discover the local marketplace from `~/.claude/plugins/known_marketplaces.json` (directory-type entry)
2. Identify the plugin: locate `plugins/{name}/.claude-plugin/plugin.json`
3. Determine current version, compute new version (semver bump)
4. Update version in `plugin.json` and `marketplace.json`
5. Clear stale cache: remove `~/.claude/plugins/cache/{marketplace}/{plugin-name}/`
6. Reinstall the plugin from `{marketplace}`
7. Verify: confirm new version in `installed_plugins.json`, list cached skills
8. Offer to commit the version bump to git (local commit only)

## Output

- Updated `plugin.json` and `marketplace.json` with new version
- Fresh cache with latest skill content
- Confirmation with version number and skill list

## Cross-References

- Use **make-a-plugin** to set up new plugins before publishing.
- Use **marketplace-publish-plugin** to publish to a remote GitHub marketplace.
