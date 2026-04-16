# Local Publish Plugin Workflow

Publish changes from a plugin inside a marketplace repo to the local Claude Code runtime. This is a local-only operation — it does not push to GitHub or any remote marketplace.

---

## Step 0: Marketplace Discovery

Determine which local marketplace to target:

1. Read `~/.claude/plugins/known_marketplaces.json`
2. Filter entries where `source.source` is `"directory"` — these are local marketplaces
3. If exactly one exists, use its key as `{marketplace}`
4. If multiple exist, present them to the user and ask which one to target
5. If none exist, abort: "No local (directory-type) marketplace found in known_marketplaces.json"
6. Confirm the marketplace directory exists: `~/.claude/plugins/marketplaces/{marketplace}/`

---

## Architecture Reference

Plugins live inside marketplace repos:

```
{marketplace-repo}/
├── .claude-plugin/
│   └── marketplace.json            # Lists all plugins with versions
└── plugins/
    └── {plugin-name}/
        ├── .claude-plugin/
        │   └── plugin.json         # Plugin metadata + version
        └── skills/
```

The `{marketplace}` runtime marketplace symlinks to each plugin directory.

### Discovering Marketplace Repos

Marketplace repos are the git repositories that contain plugin source directories. Identify them by:

1. List symlinks under `~/.claude/plugins/marketplaces/{marketplace}/plugins/`
2. Resolve each symlink target to find the parent marketplace repo path
3. Read each repo's `.claude-plugin/marketplace.json` to get repo metadata

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
1. Search across marketplace repos discovered in Step 0
2. Look for `plugins/{name}/.claude-plugin/plugin.json` in each
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
rm -rf ~/.claude/plugins/cache/{marketplace}/{plugin-name}/
```

This removes all cached versions for the plugin.

## Step 4: Reinstall

Uninstall the current version (ignore errors if not installed):

```bash
claude plugin uninstall {plugin-name}@{marketplace} 2>/dev/null || true
```

Install the latest version from the symlinked source:

```bash
claude plugin install {plugin-name}@{marketplace}
```

## Step 5: Verification

1. Read `~/.claude/plugins/installed_plugins.json`
2. Confirm entry `{plugin-name}@{marketplace}` exists
3. Confirm the version matches the new version
4. List all SKILL.md files in the new cache:
   ```bash
   find ~/.claude/plugins/cache/{marketplace}/{plugin-name}/ -name "SKILL.md"
   ```
5. Report results:

```
Plugin: {plugin-name}
Marketplace: {marketplace}
Marketplace repo: {marketplace-repo-name}
Version: {old-version} → {new-version}
Skills:
  - {skill-1}
  - {skill-2}
Cache: ~/.claude/plugins/cache/{marketplace}/{plugin-name}/{new-version}/
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
- Verify the symlink exists: `ls -la ~/.claude/plugins/marketplaces/{marketplace}/plugins/{plugin-name}`
- If missing, suggest running **make-a-plugin** first

**Install fails:**
- Check that the symlink target exists and is accessible
- Verify `plugin.json` has valid JSON syntax
- Check that `skills/` directory exists with at least one SKILL.md

**Skills not appearing after update:**
- Restart Claude Code or start a new conversation
- Skills are loaded at session start from the cache
