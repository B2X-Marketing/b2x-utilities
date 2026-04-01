# Export to Apple Notes — Workflow Reference

## HTML Tag Reference

### Supported Tags

| Element        | Tag(s)                                        | Notes                                         |
|----------------|-----------------------------------------------|-----------------------------------------------|
| Headings       | `<h1>`, `<h2>`, `<h3>`                        | Use hierarchy: h1 for title, h2 for sections  |
| Paragraph      | `<p>`                                         | Wrap all body text in `<p>` tags              |
| Bold           | `<b>`                                         | For emphasis or key terms                     |
| Italic         | `<i>`                                         | For secondary emphasis or titles              |
| Bold + Italic  | `<b><i>text</i></b>`                          | Combined emphasis                             |
| Bullet list    | `<ul><li>...</li></ul>`                       | Unordered items (renders as dots)             |
| Numbered list  | `<ol><li>...</li></ol>`                       | Ordered/sequential items                      |
| Table          | `<table><tr><th>...</th><td>...</td></table>` | Full table support with headers               |
| Colored text   | `<span style="color: #HEX">text</span>`       | Any OS-supported hex color                    |
| Font family    | `<span style="font-family: Name">text</span>` | e.g. Georgia, Helvetica, Courier New          |
| Monospace/code | `<pre><code>...</code></pre>`                 | For terminal commands and code blocks         |
| Hyperlink      | `<a href="url">label</a>`                     | Clickable links                               |
| Hashtags       | plain text inside `<p>`                       | See hashtag section below                     |

### NOT Supported — Do Not Use

| Feature         | What was tried                                | Result                           |
|-----------------|-----------------------------------------------|----------------------------------|
| Checklist       | `<ul class="checklist">`                      | Renders as plain bullet list     |
| Highlight color | `<span style="background-color: #HEX">`       | No highlight applied             |
| Indentation     | `<blockquote>`                                | No indentation applied           |
| Dashed list     | `<ul style="list-style-type: '– '">`          | Renders as dots, not dashes      |
| Markdown        | `**bold**`, `# heading`, `- item`             | Raw text, not formatted          |
| Base64 images   | `<img src="data:image/png;base64,...">`        | Stripped — image does not appear |
| Inline SVG      | `<svg>...</svg>`                              | Stripped — does not render       |

## Hashtags

Apple Notes renders `#tag` as a tappable, filterable tag — but only when written as plain text, not inside an HTML attribute or encoded. Always place tags inside a `<p>` block at the end of the note:

```html
<p>#marketing #b2x #strategy</p>
```

Do not write them as `<a href="#">#tag</a>` — that breaks the native tag behavior.

## Execution Protocol

1. **Read** the content to be posted (conversation output, document, or Claude response).
2. **Map structure** to supported HTML tags from the table above.
3. **Add hashtags** in a final `<p>` if the user specifies tags or if they are contextually obvious from the content.
4. **Call MCP** — use `add_note` or `update_note_content` with the HTML string as the `content` parameter.
5. **Confirm** to the user with the note name and folder used.

## Example Note

```html
<h1>Q2 Content Strategy</h1>

<h2>Goals</h2>
<p>Build brand authority and generate qualified leads through consistent content across
LinkedIn and The CMO Lab newsletter.</p>

<h2>Key Initiatives</h2>
<ul>
  <li><b>LinkedIn:</b> 3 posts per week — practitioner POV, no fluff</li>
  <li><b>Newsletter:</b> Bi-weekly, strategy + real results format</li>
  <li><b>AEO:</b> Publish 2 long-form reference articles per month</li>
</ul>

<h2>Success Metrics</h2>
<ol>
  <li>Newsletter subscribers: 500 by end of Q2</li>
  <li>LinkedIn followers: +300 organic</li>
  <li>Inbound leads: 5 qualified SMB contacts</li>
</ol>

<p>Reference: <a href="https://thecmolab.substack.com">The CMO Lab</a></p>

<p>#b2x #content #strategy #q2</p>
```
