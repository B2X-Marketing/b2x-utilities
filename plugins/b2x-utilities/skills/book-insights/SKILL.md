---
name: book-insights
description: >
  This skill should be used when the user says "give me insights about a book",
  "summarize this book for Notion", "extract key insights", "analyze this book",
  "add this book to my knowledge base", or wants to generate structured book
  insights and save them to the Notion Knowledge Base.
version: 1.0.0
author: Luis Capobianco
tags:
  - books
  - insights
  - notion
  - knowledge-base
  - research
---

# Book Insights

Generate the 20 most important insights from any book and save them to the Notion Knowledge Base.

Read `references/WORKFLOW.md` for the full execution protocol and prompt template.

## Inputs

- **Required:** Book title and author name
- **Optional:** Manual tag list (e.g., `Tags = Pricing, Strategy`); interactive chart add-on flag

## Workflow

1. Confirm book title and author with the user
2. Load the core prompt from `references/PROMPT_TEMPLATE.md`
3. Replace `[BOOK TITLE]` and `[AUTHOR]` placeholders
4. If user wants visual diagrams in chat, append the optional chart add-on line
5. Generate exactly 20 insights, ordered from most to least important
6. Write a title sentence and up to 1,000-word explanation per insight
7. Embed tables directly inside the insight they belong to (never grouped)
8. Prepend a short italicized blockquote summary at the top of the page
9. Append a footer line: `*Source: [Book Title] — [Author]*`
10. Save the full content to the Notion Knowledge Base using the schema in `references/NOTION_SCHEMA.md`
11. Confirm save with the Notion page URL

## Output

A fully structured Notion page in the Knowledge Base with 20 prioritized insights, embedded tables where applicable, and correct metadata (Title, Author, Category, Tags, Last Updated).

## Cross-References

- **export-to-apple-notes** — use instead of Notion if the target is Apple Notes
