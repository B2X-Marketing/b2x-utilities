# Marketplace Publish Plugin Workflow

Publish a plugin from a local marketplace repo to a remote GitHub-hosted marketplace so other users can discover and install it.

---

## Step 1: Preflight Checks

Verify all prerequisites before proceeding:

### 1a: GitHub CLI

```bash
gh --version
```

If `gh` is not installed, abort with: "Install the GitHub CLI: https://cli.github.com"

### 1b: GitHub Authentication

```bash
gh auth status
```

Confirm the user is authenticated. If not, abort with: "Run `gh auth login` to authenticate."

### 1c: GitHub Account Access

```bash
gh api user -q .login
```

Record the authenticated username for later use (determining write access to target marketplace).

---

## Step 2: Validate Plugin Source Repo

1. Confirm the plugin directory is inside a git repository:
   ```bash
   git -C {plugin-dir} rev-parse --git-dir
   ```
2. Confirm the repo has a GitHub remote:
   ```bash
   git -C {plugin-dir} remote get-url origin
   ```
3. Parse the remote URL to extract `{owner}/{repo}` (handle both HTTPS and SSH formats)
4. Verify the remote repo exists on GitHub:
   ```bash
   gh repo view {owner}/{repo} --json name -q .name
   ```

If any check fails, abort with a clear error explaining what is missing.

---

## Step 3: Validate Plugin Structure

1. Read `{plugin-dir}/.claude-plugin/plugin.json`
2. Confirm required fields exist: `name`, `version`, `description`
3. List all SKILL.md files under `{plugin-dir}/skills/`:
   ```bash
   find {plugin-dir}/skills -name "SKILL.md"
   ```
4. Confirm at least one SKILL.md exists
5. For each SKILL.md, confirm YAML frontmatter contains `name` and `description`
6. Confirm each SKILL.md has a corresponding `references/WORKFLOW.md`

Report any validation errors and abort if critical fields are missing.

---

## Step 4: Ensure Clean State

1. Check for uncommitted changes in the plugin's repo:
   ```bash
   git -C {plugin-repo} status --porcelain
   ```
2. If dirty, abort: "Commit all changes before publishing. The marketplace entry references a specific commit SHA."
3. Check that local branch is pushed to remote:
   ```bash
   git -C {plugin-repo} log @{u}..HEAD --oneline
   ```
4. If unpushed commits exist, ask user to push first or offer to push:
   ```bash
   git -C {plugin-repo} push origin {branch}
   ```

---

## Step 5: Capture Commit SHA

Record the current HEAD commit SHA — this is what the marketplace entry will reference:

```bash
git -C {plugin-repo} rev-parse HEAD
```

Store as `{commit-sha}`.

---

## Step 6: Choose Target Marketplace

1. Ask the user which GitHub marketplace repo to publish to
2. Common options:
   - `anthropics/claude-plugins-official` — Anthropic's official directory (requires PR)
   - A user-owned marketplace repo (direct commit possible)
3. Verify the target repo exists:
   ```bash
   gh repo view {marketplace-owner}/{marketplace-repo} --json name -q .name
   ```
4. Determine write access:
   ```bash
   gh api repos/{marketplace-owner}/{marketplace-repo} -q .permissions.push
   ```
   - `true` → direct commit path
   - `false` → fork + PR path

---

## Step 7: Determine Source Format

Inspect the plugin repo structure to choose the correct source type:

### Standalone plugin repo
The git repo root contains `.claude-plugin/plugin.json` directly (or the plugin is the only content).

```json
{
  "source": "url",
  "url": "https://github.com/{owner}/{repo}.git",
  "sha": "{commit-sha}"
}
```

### Plugin inside a larger repo
The plugin lives under a subdirectory (e.g., `plugins/{plugin-name}/`).

```json
{
  "source": "git-subdir",
  "url": "{owner}/{repo}",
  "path": "{relative-path-to-plugin}",
  "ref": "{branch}",
  "sha": "{commit-sha}"
}
```

Determine which format applies:
1. Find the git repo root: `git -C {plugin-dir} rev-parse --show-toplevel`
2. Compute the relative path from repo root to the plugin directory
3. If the relative path is `.` or empty → use `url` format
4. Otherwise → use `git-subdir` format

---

## Step 8: Build Marketplace Entry

Construct the JSON entry for the target marketplace's `marketplace.json`:

```json
{
  "name": "{plugin-name}",
  "description": "{description from plugin.json}",
  "category": "{category from plugin.json or 'productivity'}",
  "source": {source-object-from-step-7},
  "homepage": "https://github.com/{owner}/{repo}"
}
```

Optionally include `author` if present in `plugin.json`.

---

## Step 9: Submit to Marketplace

### Path A: Direct commit (user has write access)

1. Clone the marketplace repo to a temporary directory:
   ```bash
   gh repo clone {marketplace-owner}/{marketplace-repo} /tmp/marketplace-publish
   ```
2. Read `marketplace.json`, parse the `plugins` array
3. Check if the plugin is already listed (by name):
   - If listed: update the entry (description, source, SHA)
   - If not listed: append the new entry
4. Write the updated `marketplace.json`
5. Commit and push:
   ```bash
   git -C /tmp/marketplace-publish add .claude-plugin/marketplace.json
   git -C /tmp/marketplace-publish commit -m "feat: add {plugin-name} plugin"
   git -C /tmp/marketplace-publish push origin main
   ```
6. Clean up the temporary directory

### Path B: Fork + Pull Request (no write access)

1. Fork the marketplace repo:
   ```bash
   gh repo fork {marketplace-owner}/{marketplace-repo} --clone --remote
   ```
2. Create a branch:
   ```bash
   git checkout -b add-{plugin-name}
   ```
3. Read, update, and write `marketplace.json` (same as Path A steps 2-4)
4. Commit:
   ```bash
   git add .claude-plugin/marketplace.json
   git commit -m "feat: add {plugin-name} plugin"
   git push origin add-{plugin-name}
   ```
5. Create a pull request:
   ```bash
   gh pr create \
     --repo {marketplace-owner}/{marketplace-repo} \
     --title "Add {plugin-name} plugin" \
     --body "Adds {plugin-name}: {description}\n\nSource: https://github.com/{owner}/{repo}\nCommit: {commit-sha}"
   ```
6. Clean up the temporary directory

---

## Step 10: Verification

Report the result:

### Direct commit
```
Plugin: {plugin-name}
Marketplace: {marketplace-owner}/{marketplace-repo}
Source: https://github.com/{owner}/{repo}
Commit SHA: {commit-sha}
Status: Published
```

### Pull request
```
Plugin: {plugin-name}
Marketplace: {marketplace-owner}/{marketplace-repo}
Source: https://github.com/{owner}/{repo}
Commit SHA: {commit-sha}
PR: {pr-url}
Status: Awaiting review
```

---

## Troubleshooting

**`gh` not found:**
- Install from https://cli.github.com
- On macOS: `brew install gh`

**Not authenticated:**
- Run `gh auth login` and follow the prompts

**No GitHub remote:**
- Add one: `git remote add origin https://github.com/{owner}/{repo}.git`
- Push: `git push -u origin main`

**Uncommitted changes:**
- Commit all changes first — the marketplace entry pins a specific commit SHA
- If changes are in progress, finish and commit before publishing

**Fork already exists:**
- `gh repo fork` handles this gracefully — it reuses the existing fork
- If the branch already exists, delete it first or use a different branch name

**PR merge conflicts:**
- The marketplace maintainer may have updated `marketplace.json` since the fork
- Rebase the branch: `git pull --rebase upstream main && git push --force origin add-{plugin-name}`
