# Inbox: A Personal Claude-Powered Email Triage Agent

**Status:** Draft v0.1
**Owner:** BMad Code
**Date:** 2026-05-17
**Type:** AI-agent product (Claude as runtime)
**Audience:** BMad case-study candidate, build-and-publish track

## Vision

Mornings should not start with 87 unread emails. They should start with three decisions.

Inbox is a personal agent that wakes up before you, reads what came in overnight, sorts it, drafts your replies in your voice, archives the noise you have already pre-approved as noise, and hands you a single digest. Three buckets. Maybe five real decisions. You spend ten minutes instead of an hour.

The agent does not pretend to be you. It does not send mail under your name. It drafts, sorts, and reports. You stay in the loop on anything that leaves your outbox.

## Target user

Independent operators, founders, and senior engineers who:

- Get 50 to 300 emails a day across newsletters, vendor pings, customer threads, and real correspondence
- Have a consistent voice in their past sent mail (the agent learns from it)
- Are comfortable granting Gmail or Outlook OAuth scopes to a hosted service
- Want a trustable agent, not a magic autopilot

Not for: shared team inboxes, support queues, sales outreach automation. Those are different products.

## The agent's job

Each morning at a user-configured local time (default 6:30am), the agent autonomously:

1. Connects to Gmail or Outlook and pulls unread mail since the last run
2. Classifies each message into one of four buckets:
   - **needs-reply-from-you** (you must respond; draft prepared)
   - **FYI** (informational; marked read; no action)
   - **can-archive** (pre-approved noise; archived)
   - **needs-human-decision** (ambiguous; you triage)
3. For every needs-reply message, drafts a reply in your voice and saves it as a Gmail/Outlook draft on the thread
4. Builds a morning digest summarizing what it did and what needs you
5. Delivers the digest (email-to-self plus optional iOS push) and writes a structured audit log of every action

The user wakes up, reads the digest, opens drafts, edits or sends, and is done with email before coffee.

## Capability surface

Framed as agent behaviors, not UI features.

- **FR1.** The agent retrieves unread mail in a single batch per run, bounded by a configurable lookback window (default: since last successful run, max 24 hours).
- **FR2.** The agent classifies each message into exactly one of the four buckets using Claude with a system prompt that encodes the user's per-sender notes and voice guide.
- **FR3.** The agent auto-archives messages only from senders explicitly on the user's auto-archive allowlist. New senders never auto-archive on first contact.
- **FR4.** The agent marks FYI messages as read autonomously.
- **FR5.** The agent drafts replies for needs-reply messages and saves them via the Gmail/Outlook draft API. Drafts are attached to the correct thread.
- **FR6.** The agent never invokes the send scope. The OAuth grant requested does not include it.
- **FR7.** The agent surfaces meeting requests in needs-human-decision with proposed times pulled from your calendar's free/busy view. It does not create or accept events.
- **FR8.** The agent generates a digest summarizing counts per bucket, the three to five most important needs-human-decision items, and a link to the full audit log.
- **FR9.** The agent writes every classification and every action to durable storage with timestamps, message IDs, model version, and rationale.
- **FR10.** The user can review the last 90 days of agent actions and reverse any archive or read-marking from a simple review view.

## Trust and boundaries

This is the load-bearing section. An agent product lives or dies on what it will and will not do without asking.

**The agent will, autonomously:**

- Mark messages as read when classified FYI
- Archive messages from senders the user has explicitly added to the auto-archive allowlist
- Apply Gmail labels or Outlook categories (Inbox/Triaged/AutoArchived) for traceability
- Draft replies and save them as drafts on the thread
- Write to the audit log

**The agent will not, ever, without explicit user confirmation:**

- Send any email
- Add a sender to the auto-archive allowlist
- Unsubscribe from any list (surfaces the option in the digest; user clicks)
- Create, accept, decline, or modify calendar events
- Propose meeting times without the user reviewing them in the digest first
- Delete any message (archive is reversible; delete is not)
- Move messages to spam
- Modify contact records

**Reversibility:** Every autonomous action has an undo path. Archived messages stay searchable. Read marks can be flipped back. The audit log is the source of truth.

**Confirmation gates:**

- First-run setup requires the user to walk through a sample classification of 20 recent messages and approve the agent's logic
- Adding a sender to auto-archive requires a one-tap confirmation in the digest
- Voice guide updates require explicit approval before taking effect on future drafts

## Tool surface

Tools the agent calls during a run. Direct Anthropic API tool-use loop. No orchestration framework.

- **Gmail API** (or Outlook Graph API): read, draft, label, archive scopes only. No send scope. No delete scope.
- **Google Calendar / Outlook Calendar:** read-only free/busy scope. Used only to enrich meeting-request triage.
- **Contacts API:** read-only scope. Used to identify known senders (clients, family, vendors) and weight classification.
- **voice_corpus storage:** a per-user store containing the last 500 sent emails from the user, ingested once at setup and refreshed weekly. Used as few-shot examples in the drafting prompt.
- **Per-user memory store:** sender notes, voice guide, auto-archive allowlist, audit log. See Memory model.
- **Anthropic Messages API:** Claude Sonnet for classification and drafting. Tool-use loop is explicit and visible in the audit log.
- **Push delivery:** APNs for iOS push notification when the digest is ready. Email-to-self as a fallback channel.

## Memory model

The agent persists three categories of state, all per-user, all user-purgeable.

**Sender notes** (key-value, sender email to free-text note)

- Example: `jane@acmecorp.com` to `important client, never auto-archive, weight high`
- Example: `newsletter@stratechery.com` to `auto-archive ok`
- Updated when the user confirms an auto-archive add or edits a sender from the digest
- The agent may propose updates ("I noticed you replied to three of these. Promote this sender?") but never writes without confirmation

**Voice guide** (structured document, generated and editable)

- Auto-extracted from the voice_corpus on initial setup: typical greetings, closings, signature, average reply length, formality register, common phrases the user actually uses
- User can edit it directly. Example user-provided line: "Never use the word 'circle back.' Sign off as 'B.'"
- Used as a system-prompt block on every drafting call

**Daily log** (append-only audit table)

- Every classification, every action, every Claude call: timestamp, message ID, bucket, model version, token usage, reasoning trace
- 90-day retention by default. User-configurable up to 1 year or down to 7 days.

**Reset:** A single user action ("Forget everything") purges all three stores and revokes OAuth. The agent starts cold on the next run.

## Failure modes

- **Misclassification of an important email as can-archive.** Mitigation: auto-archive gated on explicit per-sender allowlist; new senders never auto-archive; user can review the audit log and unarchive.
- **Tone-deaf draft reply.** Mitigation: drafts only, never sends; voice guide is user-editable; the user reviews every draft.
- **Claude API outage during the morning run.** Mitigation: the agent retries with exponential backoff up to 3 times, then defers the run and notifies the user that no autonomous actions were taken.
- **Gmail rate limit hit on a large inbox.** Mitigation: batched API calls, respect Retry-After headers, fall back to partial run with a notice in the digest.
- **OAuth token expiry.** Mitigation: refresh-token flow; if refresh fails, notify the user out-of-band and pause runs until reauthorized.
- **Budget cap exceeded mid-run.** Mitigation: hard stop at the configured per-run budget; the digest reports what was processed and what was deferred to the next run.
- **A prompt-injection attempt in an inbound email.** Mitigation: the classifier is sandboxed from any tool that mutates state beyond labeling; drafts are reviewed by the user; the audit log surfaces unusual classifier behavior.

## Privacy posture

- Email content is sent to the Anthropic API on each invocation. Subject to Anthropic's standard API logging and data handling.
- No third-party logging or analytics beyond Anthropic and the hosting provider for the scheduled job.
- Per-user data (memory, audit log, voice corpus) is encrypted at rest in the hosted service and isolated per user.
- The user can export and purge all stored data at any time.
- The OAuth grant is the minimum set of scopes for the v1 capability surface. No send scope. No delete scope. No write to calendar or contacts.
- **Stretch (v2 candidate):** local-first mode where the scheduled run executes on the user's own machine or a self-hosted runner, and only inference calls leave the device.

## Constraints

- **Cost budget:** target $1.50 per user per day for the morning run, hard-capped at a user-configurable ceiling. Achieved by sending only headers and snippets for classification, full bodies only for drafting, and using prompt caching for the voice guide and sender notes.
- **Latency:** the full morning run on a 200-message inbox completes in under 4 minutes wall clock. Most of that is sequential drafting; classification batches in parallel.
- **Hosting:** the agent runs as a scheduled job on a hosted backend. The user does not need to keep a machine open. Single-region v1.
- **Rate limits:** the agent respects Gmail's per-user quotas and Anthropic's tier limits. Above-tier users can configure a higher budget ceiling.
- **Delivery:** iOS push notification when the digest is ready, via APNs. Email-to-self as the universal fallback channel. No Android app in v1.

## Non-goals

- **Not auto-replying.** The agent drafts. The user sends. This is the product's most important constraint, not a future feature.
- **Not handling calendars autonomously.** Free/busy reads only. No event mutation.
- **Not a full email client.** The user keeps using Gmail.com or Apple Mail. Inbox lives on top.
- **Not multi-user, not shared inboxes, not team triage** in v1.
- **Not Slack, Discord, SMS, or other channels** in v1. Gmail and Outlook only.
- **No LangChain, no LangGraph, no agent framework.** Direct Anthropic Messages API with an explicit tool-use loop. Per BMad style: the agent's logic is legible code, not a graph in a YAML file.
- **No "personality" layer.** The agent does not chat with the user. It does its job and reports.

## Success signal

A user we onboard in week one is still running the morning digest in week eight, and a third of their needs-reply drafts go out as sent with zero edits.

Secondary: average user time-to-inbox-zero on a weekday morning drops from a self-reported baseline (collected at onboarding) to under 15 minutes.

Tertiary: zero incidents in the first 90 days of an autonomous action the user reports as "I would not have approved this."

## Open questions

- Outlook parity in v1, or Gmail-first then Outlook-fast-follow?
- Do we offer a CLI/web review surface, or is the email digest sufficient as the only daily UI?
- How aggressively should the agent propose sender-note updates? Once per week digest, or in every digest?
- Voice guide refresh cadence: weekly auto-refresh from new sent mail, or only on user request?
- Pricing: flat monthly per user, or pass-through cost of Claude usage plus a small margin?
- Self-hosted runner as v2 stretch, or v1.1 if local-first demand is loud?
- How do we handle threads where the user has already replied from mobile between runs? Skip drafting, surface as FYI?
