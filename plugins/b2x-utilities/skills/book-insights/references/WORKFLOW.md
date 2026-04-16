# Book Insights — Workflow

Detailed execution protocol for generating 20 prioritized book insights and saving them to the Notion Knowledge Base.

---

## Phase 1: Intake

1. Identify the book title and author from the user's message
2. If either is missing, ask before proceeding
3. Ask whether the user wants the optional interactive chart add-on (creates visual diagrams in chat; cannot be saved to Notion)
4. Ask if the user wants to provide manual tags; otherwise infer from content

---

## Phase 2: Prompt Assembly

Load the core prompt from `references/PROMPT_TEMPLATE.md`.

Replace placeholders:
- `[BOOK TITLE]` → actual book title
- `[AUTHOR]` → author full name
- `[TAGS]` → user-provided tags, or `[relevant tags based on content]`

If the chart add-on is requested, append this line to the prompt:

```
For the 3 most visually important insights, also create an interactive chart or diagram in the chat conversation itself.
```

---

## Phase 3: Insight Generation

Follow these rules strictly:

| Rule | Requirement |
|------|-------------|
| Insight count | Exactly 20 |
| Order | Most to least important |
| Insight length | Up to 1,000 words per insight |
| Table placement | Embedded inside the insight it belongs to — never grouped at top or bottom |
| Table creation | Only when it genuinely adds clarity beyond the prose |
| Cell length | No cell content exceeds 10 words |
| Charts in Notion | Not supported — only embed in chat via add-on |

---

## Phase 4: Notion Page Structure

Build the Notion page in this order:

```
[Blockquote: one-paragraph italicized book summary]

---

## 1. [Insight title]

[Explanatory paragraph — up to 1,000 words]

[Table if applicable — embedded here, not at top or bottom]

---

## 2. [Insight title]

...

---

*Source: [Book Title] — [Author]*
```

---

## Phase 5: Save to Notion Knowledge Base

Use the Notion MCP to create a new page in the Knowledge Base with the schema defined in `references/NOTION_SCHEMA.md`:

- **Title** = full book title
- **Author** = author's full name
- **Category** = Reference
- **Tags** = inferred or user-provided tags
- **Last Updated** = today's date

Confirm the save with the returned Notion page URL.
