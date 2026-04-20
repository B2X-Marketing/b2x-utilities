# Notion Integration — Voice Profiles Database

B2X runs a hybrid storage model: the Markdown file in `Clients/[Client-Name]/brand-assets/` is the source of truth, and Notion mirrors it with metadata and a link back to Drive. Both must exist.

## Database

- **Name:** Voice Profiles
- **URL:** https://www.notion.so/4624446e9fe64c35bd03481416a759cc
- **Data source:** `collection://b37b472d-2713-4c64-ad7e-4e1f0e4ab201`
- **Parent page:** Creative Asset Library → B2X Marketing OS

## Properties to populate

| Property | Type | Notes |
|---|---|---|
| `Name` | title | Format: `[Client or Person Name] — [Person|Corporate] Voice` (e.g., "Quantronic Corporate Voice", "Luis Capobianco — Thought Leader Voice") |
| `Account` | relation | Link to the Accounts database entry for the client. Ask the user or search if unsure. |
| `Tone` | multi_select | Pick 2–4 from: `Professional`, `Conversational`, `Authoritative`, `Friendly`, `Technical`, `Provocative`, `Empathetic`. These must match the database options exactly. |
| `Audience` | select | Pick one: `C-Suite`, `Marketing Leaders`, `SMB Owners`, `Technical ICs`, `General Public`. |
| `Content Types` | multi_select | Pick from: `Blog Posts`, `LinkedIn Posts`, `Email Copy`, `Website Copy`, `Case Studies`, `Proposals`. |
| `Status` | select | Default `Draft`. Move to `Review` when the client reviews, `Approved` when they sign off. |
| `Notes` | text | One-paragraph summary: key phrases, banned words, top stylistic rule. Keep under 300 characters so it reads well in the table view. |
| `Profile ID` | auto_increment_id | Do not set — Notion assigns this. Read it back after creation and put it in the MD file header. |

## Page body

The body of the Notion page should contain the full profile. There are two reasonable ways to handle this:

1. **Copy the MD in full.** Best for searchability inside Notion.
2. **Summary plus Source File link.** Use when the full profile is long and will be edited in Drive regularly.

Default: copy the MD content into the Notion page body. This keeps Notion searchable and honors the hybrid storage model's "Notion Document Hub holds summary" requirement. Add the Source File URL at the top of the body in either case.

## Source File link (required)

At the top of the page body, include a Source File link back to the Drive file:

```
**Source File:** [client-slug-voice-profile-YYYY-MM-DD.md](computer:///path/to/file)
```

Or, if a Drive URL is available (e.g., Google Drive link), use that. Every Document Hub entry needs a Source File URL — this is not optional.

## Creation flow

1. Slot the draft MD into the Drive path first: `Clients/[Client-Name]/brand-assets/[client-slug]-voice-profile-YYYY-MM-DD.md`.
2. Create the Notion page in the Voice Profiles database with the properties above.
3. Capture the returned `Profile ID` (e.g., `VP-7`).
4. Edit the MD file header to include the `Profile ID`.
5. Edit the Notion page to include the Source File link to the MD.
6. Report both to the user: Drive path, Notion URL, Profile ID.

## Status transitions

- `Draft` — profile exists but the client has not reviewed it
- `Review` — the client has a link and is reviewing
- `Approved` — client signed off; safe to use as source of truth for downstream skills
- `Archived` — superseded by a newer profile (rename the old page and mark archived rather than deleting)

When updating an existing profile, create a `-v2` copy in Drive, add a new Notion entry, and move the old entry to `Archived`. Do not overwrite an Approved profile in place.

## If the Notion MCP is unavailable

Skills should degrade gracefully. If the Notion MCP is not connected:

1. Still save the MD file to Drive.
2. Tell the user: "Saved the MD. Notion MCP isn't available — here's the content to paste into the Voice Profiles database, plus the properties to set."
3. Output the properties as a ready-to-paste block.

The Drive file is the source of truth, so a missing Notion entry is recoverable. A missing Drive file is not.
