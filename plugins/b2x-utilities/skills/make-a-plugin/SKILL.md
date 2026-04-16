---
name: make-a-plugin
description: >
  Package any project repository as a Claude Code plugin and register it
  in a local marketplace. This skill should be used when the user says
  "make this a plugin", "turn this repo into a plugin", "package as plugin",
  "create plugin from repo", or wants to convert a project directory into
  an installable Claude Code plugin with skills.
version: 1.2.0
author: Luis Capobianco
tags:
  - plugin
  - packaging
  - marketplace
  - setup
---

# Make a Plugin

Package a project as a Claude Code plugin inside a marketplace repo, registered in the local marketplace.

Read `references/WORKFLOW.md` for the full execution protocol.
Read `references/CONVENTIONS.md` for SKILL.md authoring rules.

## Inputs

- **Required:** Path to the source project repo (or current working directory)
- **Optional:** Target marketplace repo; plugin name override; explicit skill list

## Workflow

1. Discover local marketplace from `~/.claude/plugins/known_marketplaces.json` (directory-type entry)
2. Analyze source repo: read CLAUDE.md, scan for existing skills, modules, docs
3. Identify candidate skills, present table to user for approval
4. Ask user which marketplace repo to add the plugin to
5. Create `plugins/{plugin-name}/.claude-plugin/plugin.json` in the marketplace repo
6. Create `plugins/{plugin-name}/skills/` with one subdirectory per skill
7. Write each SKILL.md (under ~2,100 chars) with frontmatter + `references/`
8. Move detailed content into `references/` subdirectories
9. Read-merge-write marketplace repo's and `{marketplace}`'s `marketplace.json`
10. Create symlink in `{marketplace}` to `{marketplace-repo}/plugins/{plugin-name}`
11. Clear stale cache, install via `claude plugin install {name}@{marketplace}`
12. Verify in `installed_plugins.json`, list cached skills, validate frontmatter

## Output

Plugin under `{marketplace-repo}/plugins/{plugin-name}/` with skills, registered and installed in `{marketplace}`.

## Cross-References

After creation, use **local-publish-plugin** to deploy changes locally, or **marketplace-publish-plugin** to publish to a remote marketplace.
