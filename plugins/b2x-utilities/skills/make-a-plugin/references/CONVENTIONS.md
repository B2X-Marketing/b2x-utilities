# B2X Skill Authoring Conventions

Rules for writing SKILL.md files and structuring plugin skills across all B2X projects.

---

## SKILL.md File

### Character Limit

- Total file size (frontmatter + body): under **2,100 characters**
- This keeps the skill lean and context-efficient
- Detailed procedures, templates, and schemas go in `references/`

### Frontmatter (YAML)

Required fields:

```yaml
---
name: skill-name-kebab-case
description: >
  Third-person description with quoted trigger phrases. This skill should
  be used when the user says "trigger phrase 1", "trigger phrase 2",
  "trigger phrase 3", or wants to [describe the goal].
version: 1.0.0
author: Luis Capobianco
tags:
  - relevant
  - keywords
---
```

- **name**: kebab-case, matches the directory name
- **description**: must be third-person ("This skill should be used when..."), include 3-6 quoted trigger phrases the user would say
- **version**: semver, starts at 1.0.0
- **author**: always `Luis Capobianco` for B2X skills
- **tags**: 3-6 relevant keywords

### Body Structure

1. **Heading**: `# Skill Name` (title case)
2. **Purpose**: one sentence, imperative form
3. **Reference pointer**: `Read \`references/WORKFLOW.md\` for the detailed execution protocol.`
4. **Inputs**: required and optional, bulleted
5. **Workflow**: numbered steps (summary only, 8-12 steps)
6. **Output**: what gets produced
7. **Cross-References**: related skills

### Writing Style

- Imperative/infinitive form: "Create the file", "Read the config", "Scan for modules"
- Never second person: no "You should...", "You need to...", "You can..."
- Active voice, lead with the point
- No filler words, no "delve", no unnecessary transitions
- Short and concise

---

## References Directory

Every skill must have a `references/` subdirectory containing at minimum:

- **WORKFLOW.md**: detailed step-by-step procedure (no size limit)

Optional additional references:

- **DATA_SCHEMA.md**: JSON output structure with field documentation
- **REPORT_TEMPLATE.md**: markdown output template
- **templates/**: directory with file templates (JSON, MD)
- **framework/**: numbered framework files for sequential discovery
- Domain-specific reference files

### Reference File Style

- Same writing style as SKILL.md (imperative, no second person)
- Use code blocks for file paths, commands, and JSON structures
- Include examples where they clarify the procedure
- Break into numbered phases or steps with clear headings

---

## Naming Conventions

### Marketplace Repos
- Pattern: `b2x-marketplace-{domain}` (e.g., `b2x-marketplace-marketing`, `b2x-marketplace-utilities`)

### Plugins
- Kebab-case, lowercase
- Prefix with `b2x-` for B2X Marketing plugins
- Examples: `b2x-utilities`, `strategy`, `search-intelligence`

### Skills
- Kebab-case, lowercase
- Descriptive action or noun: `make-a-plugin`, `market-research`, `swot-analysis`
- Match the directory name exactly

## Directory Layout

```
b2x-marketplace-{domain}/              # Marketplace repo (git)
├── .claude-plugin/
│   └── marketplace.json                # Lists all plugins
├── CLAUDE.md
└── plugins/
    └── {plugin-name}/                  # One per plugin
        ├── .claude-plugin/
        │   └── plugin.json             # Plugin metadata
        └── skills/
            └── {skill-name}/
                ├── SKILL.md            (< 2,100 chars)
                └── references/
                    ├── WORKFLOW.md      (required)
                    └── ...             (optional)
```
