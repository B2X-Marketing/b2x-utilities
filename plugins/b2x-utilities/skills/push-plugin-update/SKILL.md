---
name: push-plugin-update
description: >
  Push changes from a plugin source repo so they take effect in Claude Code.
  This skill should be used when the user says "push plugin update",
  "update the plugin", "bump plugin version", "refresh plugin cache",
  "reinstall plugin", "deploy plugin changes", or has modified a plugin's
  source repo and needs the changes reflected in the runtime.
version: 1.0.0
author: Luis Capobianco
tags:
  - plugin
  - update
  - deployment
  - cache
---

# Push Plugin Update

Deploy changes from a plugin source repository to the Claude Code runtime.

Read `references/WORKFLOW.md` for the detailed execution protocol.

## Inputs

- **Required:** Plugin name or path to source repo (default: current working directory)
- **Optional:** Version bump type — `patch` (default), `minor`, or `major`; skip-bump flag for cache-only refresh

## Workflow

1. Identify the plugin: read `.claude-plugin/plugin.json` from repo root
2. Determine current version, compute new version (semver bump)
3. Update version in `.claude-plugin/plugin.json`
4. Clear stale cache: remove `~/.claude/plugins/cache/b2x-local/{plugin-name}/`
5. Reinstall the plugin from b2x-local marketplace
6. Verify: confirm new version in `installed_plugins.json`, list cached skills
7. Offer to commit the version bump to git

## Output

- Updated `plugin.json` with new version
- Fresh cache with latest skill content
- Confirmation with version number and skill list

## Cross-References

- Use **make-a-plugin** first to set up new plugins before pushing updates.
