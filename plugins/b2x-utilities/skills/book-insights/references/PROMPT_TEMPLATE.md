# Book Insights — Core Prompt Template

Copy this prompt, replace all `[PLACEHOLDERS]`, and send to Claude.

---

## Core Prompt

```
Give me the 20 most important insights about [BOOK TITLE] by [AUTHOR].

Prioritize them from most to least important.

For each insight:
- Write a title sentence that summarizes the insight in one line
- Write a clear explanation of up to 1000 words
- If the insight can be meaningfully illustrated with a table or chart,
  include it directly inside that insight section — not grouped at the
  top or bottom of the document. The table must belong to the insight
  it explains and appear immediately after the explanatory paragraph.

Formatting rules for tables:
- Only create a table if it genuinely adds clarity beyond what the
  prose already provides
- Tables must be embedded within the insight they belong to,
  never in a separate reference section
- Table columns should be concise — no cell should exceed 10 words

After generating all 20 insights, save the full content as a new item
in my Notion Knowledge Base with:
- Title = [BOOK TITLE]
- Author = [AUTHOR]
- Category = Reference
- Tags = [TAGS]
- Last Updated = today's date

Structure the Notion page as follows:
1. A short italicized summary of the book as a blockquote at the top
2. The 20 numbered insight sections, each containing:
   a. An H2 heading with the insight title
   b. The explanatory paragraph
   c. Any relevant table embedded directly below the paragraph
3. A footer line: *Source: [BOOK TITLE] — [AUTHOR]*
```

---

## Optional Chart Add-On

Append this line to the core prompt if interactive visual diagrams are also wanted in the chat (note: these cannot be saved to Notion):

```
For the 3 most visually important insights, also create an
interactive chart or diagram in the chat conversation itself.
```

---

## Example (Filled In)

```
Give me the 20 most important insights about Never Split the Difference
by Chris Voss. Prioritize them from most to least important. For each
insight: write a title sentence, write a clear explanation of up to 1000
words, and if the insight can be meaningfully illustrated with a table,
include it directly inside that insight section immediately after the
explanatory paragraph. Tables must be embedded within the insight they
belong to, never in a separate reference section. Table columns should
be concise — no cell should exceed 10 words. After generating all 20
insights, save the full content as a new item in my Notion Knowledge
Base with Title = Never Split the Difference, Author = Chris Voss,
Category = Reference, Tags = Strategy, Negotiation, Last Updated = today's date.
Structure the Notion page with a short blockquote summary at the top,
the 20 numbered H2 insight sections each containing the paragraph and
any embedded table, and a footer line: *Source: Never Split the
Difference — Chris Voss*.
```
