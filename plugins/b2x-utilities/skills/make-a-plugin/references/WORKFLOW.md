# Make-a-Plugin Workflow

Complete procedure for packaging a project as a Claude Code plugin inside a marketplace repo.

---

## Phase 0: Marketplace Discovery

Determine which local marketplace to target:

1. Read `~/.claude/plugins/known_marketplaces.json`
2. Filter entries where `source.source` is `"directory"` — these are local marketplaces
3. If exactly one exists, use its key as `{marketplace}`
4. If multiple exist, present them to the user and ask which one to target
5. If none exist, abort: "No local (directory-type) marketplace found in known_marketplaces.json"
6. Confirm the marketplace directory exists: `~/.claude/plugins/marketplaces/{marketplace}/`

---

## Architecture Overview

Plugins live inside marketplace repos, not inside project repos:

```
{marketplace-repo}/
├── .claude-plugin/
│   └── marketplace.json            # Lists all plugins in this marketplace
└── plugins/
    └── {plugin-name}/              # One directory per plugin
        ├── .claude-plugin/
        │   └── plugin.json         # Plugin metadata
        └── skills/
            └── {skill-name}/
                ├── SKILL.md
                └── references/
```

The `{marketplace}` runtime marketplace uses symlinks that point to each plugin directory inside a marketplace repo:

```
~/.claude/plugins/marketplaces/{marketplace}/plugins/{plugin-name}
  → {marketplace-repo}/plugins/{plugin-name}
```

### Discovering Marketplace Repos

Marketplace repos are the git repositories that contain plugin source directories. Identify them by:

1. List symlinks under `~/.claude/plugins/marketplaces/{marketplace}/plugins/`
2. Resolve each symlink target to find the parent marketplace repo path
3. Read each repo's `.claude-plugin/marketplace.json` to get repo metadata and existing plugins

---

## Phase 1: Source Repo Analysis

1. Read the source project's `CLAUDE.md` (if it exists) to understand purpose, modules, and conventions
2. Scan for `.claude/skills/` directories (old-style project-level skill locations)
3. Scan for `INSTRUCTIONS.md` files in subdirectories — each indicates a module-level workflow
4. Scan for significant markdown documentation, templates, schemas, and framework files
5. Check if the project is already registered as a plugin in any marketplace repo

## Phase 2: Skill Discovery

1. Each module with an `INSTRUCTIONS.md` is a candidate skill
2. Each existing `.claude/skills/*/` directory is a candidate skill
3. Major standalone workflows documented in CLAUDE.md are candidates
4. Present candidates as a table to the user:

| # | Skill Name | Source | Description |
|---|-----------|--------|-------------|
| 1 | {name}    | {path} | {one-line}  |

5. Ask user to approve, modify, add, or remove skills before proceeding

## Phase 3: Choose Target Marketplace Repo

1. Discover available marketplace repos using the symlink resolution method from Phase 0
2. Present the discovered repos to the user:

| # | Marketplace Repo | Existing Plugins |
|---|---|---|
| 1 | {repo-name} | {plugin-1}, {plugin-2} |

3. Ask user which marketplace repo should hold this plugin
4. To get the current list of existing plugins, read each marketplace repo's `.claude-plugin/marketplace.json`

## Phase 4: Scaffold Plugin Inside Marketplace Repo

1. Derive plugin name:
   - Take the source repo directory name
   - Lowercase, kebab-case
   - Or use user-provided override
2. Create directory structure inside the target marketplace repo:
   ```
   {marketplace-repo}/plugins/{plugin-name}/
   ├── .claude-plugin/
   │   └── plugin.json
   └── skills/
   ```
3. Write `plugin.json` using the template from `templates/plugin-json.template.json`
4. Set version to `1.0.0` for new plugins
5. Write description: one sentence summarizing all capabilities

## Phase 5: Create Skills

For each approved skill:

1. Create `{marketplace-repo}/plugins/{plugin-name}/skills/{skill-name}/SKILL.md`
2. Write YAML frontmatter:
   - `name`: kebab-case skill name
   - `description`: third-person, includes quoted trigger phrases
   - `version`: `1.0.0`
   - `author`: from marketplace repo owner or user-provided
   - `tags`: relevant keywords
3. Write body in imperative form, under ~2,100 characters total:
   - One-line purpose statement
   - Pointer to `references/WORKFLOW.md`
   - Inputs (required/optional)
   - Numbered workflow steps (summary only)
   - Output description
   - Cross-references to related skills
4. Create `skills/{skill-name}/references/` directory
5. Always create `references/WORKFLOW.md` with the detailed procedure
6. Copy or adapt source content into additional reference files:
   - `INSTRUCTIONS.md` → `references/WORKFLOW.md`
   - Framework files → `references/framework/` or `references/`
   - Templates → `references/templates/`
   - Schemas → `references/` (as .md or .json)
   - Knowledge base docs → `references/`

See `references/CONVENTIONS.md` for all formatting rules.

## Phase 6: Update Marketplace Registrations

### 6a: Update the marketplace repo's own marketplace.json

1. Read `{marketplace-repo}/.claude-plugin/marketplace.json`
2. Parse the `plugins` array
3. Check if the plugin is already listed (by name):
   - If listed: update the description if changed
   - If not listed: append a new entry
4. New entry format:
   ```json
   {
     "name": "{plugin-name}",
     "source": "./plugins/{plugin-name}",
     "description": "{one-line description}",
     "version": "1.0.0",
     "author": { "name": "{author}" },
     "category": "productivity",
     "keywords": ["{keyword1}", "{keyword2}"]
   }
   ```
5. Write the updated marketplace.json back (preserve existing entries)

### 6b: Update the {marketplace} runtime marketplace

1. Read `~/.claude/plugins/marketplaces/{marketplace}/.claude-plugin/marketplace.json`
2. Check if the plugin is already listed; add or update as above
3. Entry format for `{marketplace}` (simpler — no version/author/keywords):
   ```json
   {
     "name": "{plugin-name}",
     "description": "{one-line description}",
     "source": "./plugins/{plugin-name}",
     "category": "productivity"
   }
   ```

## Phase 7: Symlink and Install

1. Create the symlink from `{marketplace}` to the marketplace repo's plugin directory:
   ```bash
   ln -sfn {marketplace-repo-absolute-path}/plugins/{plugin-name} \
     ~/.claude/plugins/marketplaces/{marketplace}/plugins/{plugin-name}
   ```
2. Clear any stale cache:
   ```bash
   rm -rf ~/.claude/plugins/cache/{marketplace}/{plugin-name}/
   ```
3. Uninstall if previously installed (ignore errors):
   ```bash
   claude plugin uninstall {plugin-name}@{marketplace} 2>/dev/null || true
   ```
4. Install:
   ```bash
   claude plugin install {plugin-name}@{marketplace}
   ```

## Phase 8: Verification

1. Read `~/.claude/plugins/installed_plugins.json`
2. Confirm entry `{plugin-name}@{marketplace}` exists with the correct version
3. List all SKILL.md files in the cache directory:
   ```bash
   find ~/.claude/plugins/cache/{marketplace}/{plugin-name}/ -name "SKILL.md"
   ```
4. For each SKILL.md:
   - Confirm YAML frontmatter has `name` and `description` fields
   - Confirm total file size is under 2,200 characters
5. Report success with summary:

| Skill | Status | Size |
|-------|--------|------|
| {name} | OK | {chars} chars |

6. Remind user: restart Claude Code or start a new session for skills to appear
