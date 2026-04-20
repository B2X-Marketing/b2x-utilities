# Voice Profile Interview — Detailed Workflow

This is the full workflow referenced from SKILL.md. Read this at the start of every interview.

## Why this skill is short

The original 100-question "Taste Interviewer" prompt is thorough but has two problems for B2X:

1. It asks personal questions (politics, religion, contrarian beliefs) that B2X neither needs nor should own for a client relationship.
2. Clients rarely finish it. A profile we never get is worse than a decent profile we actually ship.

This skill caps client-facing interaction at **25 questions maximum**, and typically lands at 10–15 because research pre-fills most answers. You do the homework; the client fills the gaps.

## Two tracks

Ask the user which applies before starting. If it is not obvious, ask.

- **Person track** — an individual author (founder, exec, consultant, thought leader). Voice reflects their professional identity, not their political or personal beliefs.
- **Company track** — a brand voice shared across authors and content types. Voice reflects brand positioning, not any single employee.

Track selection determines which interview bank and template to use:

- Person → `person-interview.md` + `profile-template-person.md`
- Company → `company-interview.md` + `profile-template-company.md`

## Phase 1 — Confirm subject and sources

Confirm with the user:

- Active client name and type (person or company)
- Person subject: full name and role, if track is person
- Available sources: website URL, LinkedIn URL, Company Profile doc, brand-assets folder, any other samples

If the active client is not clear from the conversation, ask first. B2X convention: never write files without knowing the target client.

## Phase 2 — Deep research (you do the homework)

Read `research-checklist.md` for the full source list and what to extract. Pull everything reasonably available. Produce a short internal research summary with:

- Observed tone (3–5 descriptors)
- Sentence length and rhythm signals
- Vocabulary they reach for and vocabulary they avoid
- Structural patterns (openings, closings, use of lists)
- Audience and content types observed
- Candidate "always" and "never" rules visible in the work
- Gaps research could not fill → these become the questions you actually ask

## Phase 3 — Draft the profile before asking

Open the right template and fill every field with one of three states:

- `[confirmed from research]` — strong evidence in the sources
- `[inferred — needs confirmation]` — plausible but uncertain
- `[missing — must ask]` — no signal

This draft is what you will walk the client through. Most of it is already filled.

## Phase 4 — The interview (≤25 questions)

Load the appropriate question bank. Rules:

1. **One question per turn.** Wait for the answer. Conversation, not form.
2. **Skip what research already answered.** Do not re-ask confirmed fields. Surface the observation in the final review instead.
3. **Confirm inferred fields.** Frame as "From your website I see X — does that sound right?" Confirmations count toward the 25.
4. **Ask missing fields fully.**
5. **Push back on vague answers.** If "professional but friendly," ask for a sentence that fits and one that does not.
6. **No personal ideology.** No politics, religion, worldview, contrarian social takes. Work voice only.
7. **Never exceed 25 client-facing questions.** If you are at 20 and still have gaps, pick the highest-leverage remaining ones and drop the rest. The profile supports `Unresolved` fields explicitly.

Track the count. Every client-facing question counts, including confirmations and follow-ups.

## Phase 5 — Show the draft and approve

Present the completed profile to the client. Ask for:

- Anything missing
- Anything wrong
- The three rules that matter most (these become the Quick Reference Card)

Iterate once or twice. Then lock.

## Phase 6 — Save outputs

Two destinations, in this order. Do both unless the user says otherwise.

**1. Markdown in client folder**

Path: `Clients/[Client-Name]/brand-assets/[client-slug]-voice-profile-YYYY-MM-DD.md`

Today's date. Client slug lowercase-with-hyphens. Naming matches B2X convention.

**2. Notion Voice Profiles database entry**

Read `notion-integration.md` for schema, properties, and Source File linking. Default `Status` is `Draft` until the client explicitly approves.

## Phase 7 — Report back

Tell the user:

- Where the MD lives (client folder path)
- Notion Profile ID and URL
- How many questions the client answered (vs. the 25 cap)
- Current status

## Default rules baked into every profile

If the client does not weigh in, apply B2X defaults from `Clients/B2X-HQ/CLAUDE.md`:

- Active voice, lead with the point
- No "delve", no filler transitions
- Short, direct sentences
- Numbers as digits (except sentence start)
- Spell out acronyms on first use
- Inline source attribution, not bracket citations

Document any default the client explicitly overrode in the profile.

## What success looks like

A voice profile that:

- Fits in 2–4 pages
- Has concrete examples, not vague adjectives
- Has at least 3 "always" and 3 "never" rules
- Is specific enough that another Claude instance can apply it without re-interviewing the client
- Was produced with ≤25 client-facing questions
