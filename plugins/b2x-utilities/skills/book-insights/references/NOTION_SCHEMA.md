# Notion Knowledge Base — Schema Reference

Target database: **Knowledge Base** in the connected Notion workspace.

---

## Properties

| Property     | Type         | Value                                      |
|--------------|--------------|--------------------------------------------|
| Title        | Title        | Full book title                            |
| Author       | Text         | Author full name                           |
| Category     | Select       | `Reference`                                |
| Tags         | Multi-select | Inferred from content or user-provided     |
| Last Updated | Date         | Today's date (ISO 8601)                    |

---

## Available Tags

| Tag          | Use when the book covers...                  |
|--------------|----------------------------------------------|
| Pricing      | Pricing strategy, monetization, value-based  |
| Strategy     | Business strategy, competitive positioning   |
| Buyer        | Buyer psychology, purchasing decisions       |
| StoryTelling | Narrative, communication, persuasion         |

Add tags beyond this list if the content clearly warrants them.

---

## Page Body Structure

```
[Blockquote: one-paragraph italicized summary]

---

## 1. [Insight Title]

[Explanatory paragraph — up to 1,000 words]

[Table if applicable]

---

## 2. [Insight Title]

...

---

*Source: [Book Title] — [Author]*
```
