---
name: export-to-apple-notes
description: >
  Apply rich HTML formatting when creating or updating notes in Apple Notes via the
  MCP connector. Use this skill whenever the user asks to post, add, save, or update
  content in Apple Notes — even if they don't mention formatting. Always apply HTML
  structure by default; never post plain text.
version: 1.0.0
author: Luis Capobianco
tags:
  - apple-notes
  - export
  - formatting
  - html
---

# Export to Apple Notes

Format and post content to Apple Notes with proper HTML rendering via the MCP connector.

Read `references/WORKFLOW.md` for the full formatting reference and execution protocol.

## Inputs

- **Required:** Content to post (from conversation, document, or Claude response)
- **Optional:** Target folder name, hashtags, note title

## Supported HTML Tags

Headings (`h1`–`h3`), paragraphs (`p`), bold (`b`), italic (`i`), bullet lists (`ul/li`), numbered lists (`ol/li`), tables (`table/tr/th/td`), colored text (`span style="color"`), font family (`span style="font-family"`), code blocks (`pre/code`), hyperlinks (`a href`), hashtags (plain text in `p`).

## NOT Supported

Checklists, highlight/background color, blockquote indentation, dashed lists, Markdown syntax, base64 images, inline SVG.

## Workflow

1. Read the content to be posted
2. Map structure to supported HTML tags (see `references/WORKFLOW.md`)
3. Add `#hashtags` in a final `<p>` block if specified or contextually obvious
4. Call `add_note` or `update_note_content` with the HTML string as `content`
5. Confirm to the user with the note name and folder used

## Formatting Rules

- Always use HTML — never plain text or Markdown
- Wrap everything in tags — even single sentences go in `<p>`
- Mirror source structure — preserve headings, bold, lists from the original
- Place hashtags at the bottom in a final `<p>` block
- No `<html>`, `<body>`, or `<head>` wrappers — pass inner HTML only

## Cross-References

Pairs with any skill that produces output the user wants saved to Apple Notes.
