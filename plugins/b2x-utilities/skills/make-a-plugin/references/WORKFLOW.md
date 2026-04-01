# Make-a-Plugin Workflow

Complete procedure for converting a B2X project repository into a Claude Code plugin.

---

## Phase 1: Repo Analysis

1. Read the repo's `CLAUDE.md` (if it exists) to understand purpose, modules, and conventions
2. Scan for `.claude/skills/` directories (old-style project-level skill locations)
3. Scan for `INSTRUCTIONS.md` files in subdirectories — each indicates a module-level workflow
4. Scan for significant markdown documentation, templates, schemas, and framework files
5. Check if `.claude-plugin/plugin.json` already exists (skip creation if so)
6. Check if a top-level `skills/` directory already exists (merge/update rather than overwrite)

## Phase 2: Skill Discovery

1. Each module with an `INSTRUCTIONS.md` is a candidate skill
2. Each existing `.claude/skills/*/` directory is a candidate skill
3. Major standalone workflows documented in CLAUDE.md are candidates
4. Present candidates as a table to the user:

| # | Skill Name | Source | Description |
|---|-----------|--------|-------------|
| 1 | {name}    | {path} | {one-line}  |

5. Ask user to approve, modify, add, or remove skills before proceeding

## Phase 3: Scaffold Plugin Structure

1. Create `.claude-plugin/plugin.json` at repo root using the template from `templates/plugin-json.template.json`
2. Derive plugin name:
   - Take the repo directory name
   - Lowercase, kebab-case
   - Prepend `b2x-` if not already present (e.g., `Strategy` → `b2x-strategy`)
   - Or use user-provided override
3. Set version to `1.0.0` for new plugins
4. Write description: one sentence summarizing all capabilities

## Phase 4: Create Skills

For each approved skill:

1. Create `skills/{skill-name}/SKILL.md`
2. Write YAML frontmatter:
   - `name`: kebab-case skill name
   - `description`: third-person, includes quoted trigger phrases (e.g., "This skill should be used when the user says...")
   - `version`: `1.0.0`
   - `author`: `Luis Capobianco`
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

## Phase 5: Update CLAUDE.md

If the repo already has a `CLAUDE.md`:
- Append a `## Plugin Structure` section with:
  - Plugin name and version
  - Table of skills (name, trigger, description)
  - Directory tree showing the `skills/` layout

If no `CLAUDE.md` exists:
- Create one with:
  - Project name and purpose (one paragraph)
  - Plugin structure section (as above)
  - Output style rules (active voice, no filler, no "delve")

## Phase 6: Marketplace Registration

1. Read the current marketplace config:
   ```
   ~/.claude/plugins/marketplaces/b2x-local/.claude-plugin/marketplace.json
   ```
2. Parse the `plugins` array
3. Check if the plugin is already listed (by name):
   - If listed: update the description if changed
   - If not listed: append a new entry
4. New entry format:
   ```json
   {
     "name": "{plugin-name}",
     "description": "{one-line description}",
     "source": "./plugins/{plugin-name}",
     "category": "productivity"
   }
   ```
5. Write the updated marketplace.json back (preserve existing entries)

## Phase 7: Symlink and Install

1. Create the symlink:
   ```bash
   ln -sfn {repo-absolute-path} ~/.claude/plugins/marketplaces/b2x-local/plugins/{plugin-name}
   ```
2. Clear any stale cache:
   ```bash
   rm -rf ~/.claude/plugins/cache/b2x-local/{plugin-name}/
   ```
3. Uninstall if previously installed (ignore errors):
   ```bash
   claude plugin uninstall {plugin-name}@b2x-local 2>/dev/null || true
   ```
4. Install:
   ```bash
   claude plugin install {plugin-name}@b2x-local
   ```

## Phase 8: Verification

1. Read `~/.claude/plugins/installed_plugins.json`
2. Confirm entry `{plugin-name}@b2x-local` exists with the correct version
3. List all SKILL.md files in the cache directory:
   ```bash
   find ~/.claude/plugins/cache/b2x-local/{plugin-name}/ -name "SKILL.md"
   ```
4. For each SKILL.md:
   - Confirm YAML frontmatter has `name` and `description` fields
   - Confirm total file size is under 2,200 characters
5. Report success with summary:

| Skill | Status | Size |
|-------|--------|------|
| {name} | OK | {chars} chars |

6. Remind user: restart Claude Code or start a new session for skills to appear in the available skills list
