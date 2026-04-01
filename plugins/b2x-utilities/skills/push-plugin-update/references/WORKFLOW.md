# Push Plugin Update Workflow

Deploy changes from a plugin inside a marketplace repo so they take effect in the Claude Code runtime.

---

## Architecture Reference

Plugins live inside marketplace repos:

```
b2x-marketplace-{domain}/
├── .claude-plugin/
│   └── marketplace.json            # Lists all plugins with versions
└── plugins/
    └── {plugin-name}/
        ├── .claude-plugin/
        │   └── plugin.json         # Plugin metadata + version
        └── skills/
```

The `b2x-local` runtime marketplace symlinks to each plugin directory.

### Known Marketplace Repos

| Repo | Location |
|---|---|
| `b2x-marketplace-marketing` | `Projects/GitHub/b2x-marketplace-marketing` |
| `b2x-marketplace-utilities` | `Projects/GitHub/b2x-marketplace-utilities` |

---

## Step 1: Plugin Identification

Determine which plugin to update:

**If run from a marketplace repo directory:**
1. List directories under `plugins/`
2. If only one plugin exists, use it automatically
3. If multiple plugins exist, ask the user which one to update
4. Read `plugins/{plugin-name}/.claude-plugin/plugin.json`
5. Extract the `name` and current `version` fields

**If given a plugin name:**
1. Search across known marketplace repos for `plugins/{name}/.claude-plugin/plugin.json`
2. Check `b2x-marketplace-marketing` and `b2x-marketplace-utilities`
3. Read the matching `plugin.json`

**If run from inside a plugin subdirectory** (e.g., inside `plugins/{name}/`):
1. Look for `.claude-plugin/plugin.json` relative to the current directory
2. Determine the parent marketplace repo from the path

Fail with a clear error if `plugin.json` is not found.

## Step 2: Version Bump

Parse the current version as semver (MAJOR.MINOR.PATCH):

| Bump Type | Rule | Example |
|-----------|------|---------|
| `patch` (default) | Increment PATCH | 1.0.0 → 1.0.1 |
| `minor` | Increment MINOR, reset PATCH | 1.0.3 → 1.1.0 |
| `major` | Increment MAJOR, reset MINOR+PATCH | 1.2.3 → 2.0.0 |

If the user requests skip-bump (cache-only refresh):
- Keep the current version unchanged
- Still proceed with cache clearing and reinstall

Update the version in two places:
1. `plugins/{plugin-name}/.claude-plugin/plugin.json` — the `version` field
2. `{marketplace-repo}/.claude-plugin/marketplace.json` — the `version` field in the matching plugin entry (if present)

## Step 3: Cache Invalidation

Clear the stale cached copy so Claude Code picks up the latest source:

```bash
rm -rf ~/.claude/plugins/cache/b2x-local/{plugin-name}/
```

This removes all cached versions for the plugin.

## Step 4: Reinstall

Uninstall the current version (ignore errors if not installed):

```bash
claude plugin uninstall {plugin-name}@b2x-local 2>/dev/null || true
```

Install the latest version from the symlinked source:

```bash
claude plugin install {plugin-name}@b2x-local
```

## Step 5: Verification

1. Read `~/.claude/plugins/installed_plugins.json`
2. Confirm entry `{plugin-name}@b2x-local` exists
3. Confirm the version matches the new version
4. List all SKILL.md files in the new cache:
   ```bash
   find ~/.claude/plugins/cache/b2x-local/{plugin-name}/ -name "SKILL.md"
   ```
5. Report results:

```
Plugin: {plugin-name}
Marketplace repo: {marketplace-repo-name}
Version: {old-version} → {new-version}
Skills:
  - {skill-1}
  - {skill-2}
Cache: ~/.claude/plugins/cache/b2x-local/{plugin-name}/{new-version}/
```

## Step 6: Git Commit (Optional)

If the version was bumped, offer to commit the change in the marketplace repo:

- Stage: `git add plugins/{plugin-name}/.claude-plugin/plugin.json .claude-plugin/marketplace.json`
- Message: `chore: bump {plugin-name} to v{new-version}`
- Do not push unless explicitly asked

If the user declines, leave the change unstaged.

---

## Troubleshooting

**Plugin not found in marketplace repo:**
- Verify the plugin directory exists: `ls {marketplace-repo}/plugins/{plugin-name}/`
- Verify the symlink exists: `ls -la ~/.claude/plugins/marketplaces/b2x-local/plugins/{plugin-name}`
- If missing, suggest running **make-a-plugin** first

**Install fails:**
- Check that the symlink target exists and is accessible
- Verify `plugin.json` has valid JSON syntax
- Check that `skills/` directory exists with at least one SKILL.md

**Skills not appearing after update:**
- Restart Claude Code or start a new conversation
- Skills are loaded at session start from the cache
