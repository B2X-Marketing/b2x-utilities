---
name: make-a-plugin
description: >
  Package any B2X project repository as a Claude Code plugin and register it
  in the b2x-local marketplace. This skill should be used when the user says
  "make this a plugin", "turn this repo into a plugin", "package as plugin",
  "register this in b2x-local", "create plugin from repo", or wants to convert
  any project directory into an installable Claude Code plugin with skills.
version: 1.0.0
author: Luis Capobianco
tags:
  - plugin
  - packaging
  - marketplace
  - setup
---

# Make a Plugin

Convert a B2X project repository into a Claude Code plugin registered in the b2x-local marketplace.

Read `references/WORKFLOW.md` for the full execution protocol.
Read `references/CONVENTIONS.md` for SKILL.md authoring rules.

## Inputs

- **Required:** Path to the target repo (or current working directory)
- **Optional:** Plugin name override (default: repo dir name, kebab-case); explicit skill list

## Workflow

1. Analyze the repo: read CLAUDE.md, scan for `.claude/skills/`, INSTRUCTIONS.md, modules
2. Identify candidate skills, present table to user for approval
3. Create `.claude-plugin/plugin.json` at repo root
4. Create `skills/` directory with one subdirectory per skill
5. Write each SKILL.md (under ~2,100 chars) with frontmatter + `references/`
6. Move detailed content (instructions, frameworks, templates) into `references/`
7. Update repo CLAUDE.md with plugin structure section
8. Read-merge-write marketplace.json to add the new plugin entry
9. Create symlink in `~/.claude/plugins/marketplaces/b2x-local/plugins/`
10. Clear stale cache, install via `claude plugin install {name}@b2x-local`
11. Verify in `installed_plugins.json`, list cached skills, validate frontmatter

## Output

Plugin with `.claude-plugin/plugin.json` and `skills/` tree, registered and installed in b2x-local.

## Cross-References

After creation, use **push-plugin-update** to deploy future changes.
