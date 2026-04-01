# Push Plugin Update Workflow

Deploy changes from a plugin source repository so they take effect in the Claude Code runtime.

---

## Step 1: Plugin Identification

Determine which plugin to update:

**If run from a repo directory:**
1. Read `.claude-plugin/plugin.json` from the repo root
2. Extract the `name` field — this is the plugin name
3. Extract the current `version` field

**If given a plugin name:**
1. Read `~/.claude/plugins/marketplaces/b2x-local/.claude-plugin/marketplace.json`
2. Find the entry matching the given name
3. Resolve the `source` path (relative to marketplace dir) to get the repo path
4. Follow the symlink to the actual repo
5. Read `.claude-plugin/plugin.json` from the resolved repo path

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

Write the updated version back to `.claude-plugin/plugin.json`.

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
Version: {old-version} → {new-version}
Skills:
  - {skill-1}
  - {skill-2}
Cache: ~/.claude/plugins/cache/b2x-local/{plugin-name}/{new-version}/
```

## Step 6: Git Commit (Optional)

If the version was bumped, offer to commit the change:

- Stage: `git add .claude-plugin/plugin.json`
- Message: `chore: bump {plugin-name} to v{new-version}`
- Do not push unless explicitly asked

If the user declines, leave the change unstaged.

---

## Troubleshooting

**Plugin not found in marketplace:**
- Verify the symlink exists: `ls -la ~/.claude/plugins/marketplaces/b2x-local/plugins/{plugin-name}`
- If missing, suggest running **make-a-plugin** first

**Install fails:**
- Check that the symlink target exists and is accessible
- Verify `plugin.json` has valid JSON syntax
- Check that `skills/` directory exists with at least one SKILL.md

**Skills not appearing after update:**
- Restart Claude Code or start a new conversation
- Skills are loaded at session start from the cache
