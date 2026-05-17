# BookCorner PRD

**Product:** BookCorner (Discord bot)
**Owner:** indie dev, solo
**Status:** draft v0.1
**Date:** 2026-05-17
**Platform:** Discord (bot + slash commands)

## Problem

book club servers on discord are great until the actual reading part. people start a book, three of them race ahead, two never start, someone drops a spoiler in #general, and the channel quietly dies by chapter four. there is no shared sense of where everyone is, no way to leave a thought tied to a page without spoiling someone two chapters behind, and no lightweight way to pick the next book without a 40-message thread that ends in a shrug.

mods end up duct-taping this together with pinned messages, spreadsheets, and a lot of "no spoilers please" reminders. it does not scale, and it pushes casual readers out.

## Target Community

book club servers (3 to 500 members, sometimes bigger), reading-buddy duos that live in a single channel, and the broader booktok-adjacent discord scene where reading is social but pace is wildly uneven. the common thread: people who want shared reading, not shared opinions about reading. they care about spoilers more than they care about features.

this is not for general-purpose servers that happen to have a books channel. it is for servers where reading is the point.

## Functional Requirements

- **FR1** `/read start <book> <pages_or_chapters> <pace>` registers a reading sprint in the channel it is invoked in. pace is a string (e.g. "30 pages/day", "1 chapter/week"). only one active sprint per channel.
- **FR2** `/read where` returns a public message showing each opted-in member's current page or chapter for the active book. members who have not logged progress show as "not started".
- **FR3** `/read note <text>` attaches a note to the caller's current page. notes are hidden from other members until those members log a page at or past the note's page (delayed reveal). notes render with discord's spoiler tag as a second layer of defense.
- **FR4** `/read poll <suggestions>` posts a public message with one button component per suggestion (max 5) and tallies votes. one vote per member, vote-change allowed until poll closes. poll closes via button or after 7 days.
- **FR5** `/read shelf` returns an **ephemeral** response listing the caller's reading log in the current server (books finished, currently reading, abandoned). nobody else sees it.
- **FR6** `/read spoilers off` toggles spoiler-protection for the channel. requires Manage Channel permission. default is on.
- **FR7** progress logging: members update their page via a modal triggered by a "log progress" button attached to the sprint's pinned message. no slash command needed for the common case.
- **FR8** when a sprint starts, the bot optionally creates a thread on the sprint message titled with the book name, for archival discussion. configurable per server.

## Discord Surface

**Intents requested (minimum necessary):**
- `applications.commands` (slash command registration)
- `bot` (baseline presence)
- `message_components` (buttons, selects, modals)
- explicitly **not** requesting `MESSAGE_CONTENT`. the bot does not read message text. all input flows through slash commands and components.

**Slash commands registered (all under `/read` group):**
- `/read start`
- `/read where`
- `/read note`
- `/read poll`
- `/read shelf`
- `/read spoilers`

**Permissions required (bot role):**
- Send Messages
- Send Messages in Threads
- Embed Links
- Use Application Commands
- Create Public Threads (only if thread-per-book is enabled by the server)
- Manage Messages is **not** requested. the bot does not delete user messages.

**Channel scope behavior:**
- sprint state is per-channel, not per-server. a server can have multiple book clubs across different channels.
- `/read shelf` is per-server (a member's shelf is scoped to the server they are in).
- spoiler-protection toggle is per-channel.
- the bot ignores DMs entirely in v1.

**Response posture:**
- `/read shelf` and any error messages are ephemeral.
- everything else is public to the channel.
- the bot reacts with `:books:` on successful sprint start and `:bookmark:` on a logged progress update.

## Hosting and Runtime Posture

v1 ships as a hosted free tier on a single small node (fly.io or railway, tbd). single process, websocket gateway connection, sharded only when discord requires it (>2500 guilds). storage is sqlite for v1, with a migration path to postgres when guild count or write volume forces it.

scale targets: handle servers up to 50,000 members without hitting per-channel or per-guild rate limits. interaction responses must ack within discord's 3-second window; cold-start on a slash command is budgeted at <500ms p95.

self-hosted is a stretch goal: same binary, sqlite, env-var config, one `docker run` command. not v1.

## Privacy Posture

- no `MESSAGE_CONTENT` intent. the bot cannot read what people say in chat.
- the bot stores: discord user id, guild id, channel id, current book metadata, progress entries, notes (text), poll votes. nothing else.
- no email, no real name, no avatar caching.
- `/read forget-me` (v1.1, not v1) will hard-delete a user's records in the server.
- no analytics sdk. server-side logs are structured and retained 30 days.
- no DMs to users, ever, except in response to a slash command they invoked.

## Constraints

- must support servers up to 50K members without rate-limit issues.
- cold-start latency on slash command: <500ms p95.
- discord ToS compliant: no message scraping, no bulk DMs, no user impersonation, no off-platform data sale.
- single maintainer for v1 (solo). complexity ceiling is whatever one person can keep in their head on a saturday.

## Non-Goals

- no web companion app or dashboard in v1.
- no goodreads or storygraph integration in v1. manual book entry, optional isbn lookup via openlibrary only.
- no AI chat surface. the temptation is huge. we are deliberately not.
- no cross-server reading. a server's book club is its own world.
- no monetization in v1. no premium tier, no patreon gate, no "pro" commands.
- no voice channel features.
- no leaderboards (this is a book club, not a competition).

## Success Signal

within 60 days of launch: 50 servers with at least one completed sprint (start to last-page logged by at least 2 members). qualitative signal: mods saying "we stopped using the pinned-message spreadsheet."

leading indicator at week 2: median sprint has 3+ members logging progress at least twice.

## Open Questions

- pace string: free-form text or structured input (number + unit)? free-form is friendlier, structured is queryable.
- notes: cap on length? cap on count per book per user?
- what happens to in-progress sprints when a user leaves the server? freeze, anonymize, or delete?
- thread-per-book: opt-in per server, opt-in per sprint, or always on with a toggle?
- isbn lookup: openlibrary only, or fall back to google books? openlibrary is friendlier to rate-limit-shy free tiers.
- when a poll ties, what is the tiebreaker? extend by 24 hours, mod picks, or random?
