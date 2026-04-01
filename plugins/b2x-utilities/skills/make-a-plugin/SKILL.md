---
name: make-a-plugin
description: >
  Package any B2X project repository as a Claude Code plugin and register it
  in the b2x-local marketplace. This skill should be used when the user says
  "make this a plugin", "turn this repo into a plugin", "package as plugin",
  "register this in b2x-local", "create plugin from repo", or wants to convert
  any project directory into an installable Claude Code plugin with skills.
version: 1.1.0
author: Luis Capobianco
tags:
  - plugin
  - packaging
  - marketplace
  - setup
---

# Make a Plugin

Package a B2X project as a Claude Code plugin inside a marketplace repo, registered in b2x-local.

Read `references/WORKFLOW.md` for the full execution protocol.
Read `references/CONVENTIONS.md` for SKILL.md authoring rules.

## Inputs

- **Required:** Path to the source project repo (or current working directory)
- **Optional:** Target marketplace repo (default: ask user); plugin name override (default: repo dir name, kebab-case); explicit skill list

## Workflow

1. Analyze the source repo: read CLAUDE.md, scan for existing skills, modules, docs
2. Identify candidate skills, present table to user for approval
3. Ask user which marketplace repo to add the plugin to (b2x-marketplace-marketing or b2x-marketplace-utilities)
4. Create `plugins/{plugin-name}/.claude-plugin/plugin.json` inside the marketplace repo
5. Create `plugins/{plugin-name}/skills/` with one subdirectory per skill
6. Write each SKILL.md (under ~2,100 chars) with frontmatter + `references/`
7. Move detailed content into `references/` subdirectories
8. Read-merge-write the marketplace repo's `.claude-plugin/marketplace.json`
9. Read-merge-write `b2x-local` marketplace.json to add the new plugin entry
10. Create symlink in b2x-local pointing to `{marketplace-repo}/plugins/{plugin-name}`
11. Clear stale cache, install via `claude plugin install {name}@b2x-local`
12. Verify in `installed_plugins.json`, list cached skills, validate frontmatter

## Output

Plugin under `{marketplace-repo}/plugins/{plugin-name}/` with skills, registered and installed in b2x-local.

## Cross-References

After creation, use **push-plugin-update** to deploy future changes.
