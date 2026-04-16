---
name: marketplace-publish-plugin
description: >
  Publish a plugin to a remote GitHub-hosted marketplace. This skill should
  be used when the user says "publish to marketplace", "publish plugin to
  GitHub", "submit plugin to marketplace", "push plugin to remote", or wants
  to make a locally developed plugin available in a GitHub marketplace repo.
version: 1.0.0
author: Luis Capobianco
tags:
  - plugin
  - publish
  - marketplace
  - github
---

# Marketplace Publish Plugin

Publish a plugin from a local marketplace repo to a remote GitHub-hosted marketplace so other users can install it. Validates prerequisites, determines the correct source format, and submits via commit or pull request.

Read `references/WORKFLOW.md` for the detailed execution protocol.

## Inputs

- **Required:** Plugin name or path to the plugin directory
- **Optional:** Target marketplace repo (default: ask user); commit message override

## Workflow

1. **Preflight checks**: verify `gh` CLI installed, authenticated, and has repo access
2. Verify the plugin directory is inside a git repo with a GitHub remote
3. Validate plugin structure: `plugin.json`, at least one SKILL.md, valid frontmatter
4. Ensure working tree is clean — all plugin changes committed and pushed
5. Determine the current commit SHA from the plugin's repo
6. Ask user which GitHub marketplace repo to publish to
7. Determine source format: `url` (standalone repo) or `git-subdir` (plugin inside larger repo)
8. Build the marketplace entry JSON with source URL, SHA, description
9. Submit: direct commit to owned marketplace, or fork + PR to third-party marketplace
10. Verify the submission landed (commit or PR URL)

## Output

- Marketplace entry added or updated in the target GitHub marketplace repo
- Commit SHA or pull request URL confirming the submission

## Cross-References

- Use **make-a-plugin** to create the plugin structure first.
- Use **local-publish-plugin** for local-only cache refresh without remote publishing.
