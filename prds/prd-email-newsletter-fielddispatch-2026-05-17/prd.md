# FieldDispatch PRD

- Product: FieldDispatch
- Surface: Email (inbox is the runtime)
- Region (v1): Colorado, USA
- Audience: Outdoor and public-lands enthusiasts
- Author: Founder-operator
- Date: 2026-05-17
- Status: Draft v1

## Problem

People who care about Colorado's public lands check five places every morning to plan their day: a weather app, USFS alert pages, CPW pages, a tide-of-information Reddit, and whatever bookmark holds the trailhead they care about. The signal is buried. The cadence is broken. Nobody glances at five tabs at 5:45am. The result: people miss the fire ban, miss the ice-off, miss the draw deadline, miss the window. The information exists. The delivery is the gap.

The reader does not want another app to install. They do not want a feed. They already have inbox habits at sunrise. The opportunity is to meet them there with one well-crafted message and earn the open every day on quality alone.

## Target reader

A Colorado-based hiker, angler, hunter, camper, or climber who already has 2 to 6 "home areas" they care about (a forest, a wilderness, a stretch of river, a unit). They open email on phone in bed or over coffee. They want a 60-second briefing, not a feed. They distrust apps that monetize their attention. They are happy with a sponsorship-funded newsletter that respects their time.

## Functional requirements

- FR1: Subscriber can set up to 3 saved areas (point or named area) and 1 to 3 activity interests (hike, fish, hunt, camp, climb).
- FR2: System sends one personalized email per subscriber per morning, dispatched to land in the inbox by 6:00am MT.
- FR3: Email contains, in order: today's weather for the 3 saved areas, one curated condition update, one regulatory or seasonal item, sunrise / sunset / moonset for the primary area, one in-season recommendation.
- FR4: All AI-generated claims are traceable to a fetched source URL captured at generation time.
- FR5: Subscriber can change saved areas, interests, or unsubscribe from any link in any email.
- FR6: Operator can review and approve, edit, or block the day's content from the admin app before send.
- FR7: System auto-sends if no content items are flagged by the hallucination check or operator review queue.
- FR8: One-click unsubscribe works without account login.

## Email rendering surface

- Width: 600px max, single-column.
- Layout: nested HTML tables only. No flex, no grid, no CSS variables in send.
- CSS: inline on every element that styles. Style block in head as a progressive enhancement, not a dependency.
- Dark mode: handled via `prefers-color-scheme` media query and `@media (prefers-color-scheme: dark)` block; foreground and background tokens swap; no inverted logos.
- Images: optional. No image is required to read the email. Every image has alt text that carries the meaning. Many subscribers block images by default; the email reads complete without them.
- Size budget: total HTML payload under 102KB to avoid Gmail clipping.
- Plain-text alternative: every send includes a hand-shaped (not auto-stripped) text/plain part.
- Accessibility: real h1 / h2 elements, semantic list elements for the bullets, alt text on every img, sufficient contrast in both light and dark, no all-image emails, no text baked into images.
- Client floor: render must be acceptable in Gmail (web, iOS, Android), Apple Mail (macOS, iOS), Outlook 2019+, and Yahoo Mail.

## Deliverability posture

- Authentication: SPF, DKIM, and DMARC published at the sending domain with DMARC policy set to `p=reject` after a 30-day monitoring window.
- Sending infrastructure: dedicated IP via Postmark or Resend; subdomain isolated from any transactional mail.
- Warm-up: scheduled IP warm-up plan ramping from 50 to 50,000 daily sends over 6 weeks before public launch.
- Bounce handling: hard bounce removes immediately; soft bounce three strikes within 14 days then suppress; spam complaint removes immediately and permanently.
- Unsubscribe: RFC 8058 one-click List-Unsubscribe header and visible unsubscribe link in every email.
- Sunset policy: 90 consecutive days of non-opens moves the address to a re-engagement track; one re-engagement send; if no open, suppress.
- Content hygiene: no link shorteners, no tracking pixels beyond a single open beacon, no attachments.

## Personalization

- Segmentation axes: saved areas (geographic), activity interests, license-holder status (self-reported, opt-in), and tenure (new subscribers receive a 3-email onboarding sequence).
- Merge tags: first name (optional), primary area display name, secondary area display names, sunrise / sunset / moonset values, weather summary blocks, in-season item.
- Content selection: condition update and regulatory item are scored per subscriber against their saved areas and interests; ties broken by recency.
- Fallback: if a subscriber has no saved areas, default to a statewide briefing for their activities.

## Operator workflow

- Single admin web app, single operator (the publisher).
- Each morning at 4:30am MT, the day's draft content is generated, hallucination-checked against fetched source URLs, and queued for review.
- Operator dashboard shows: items passing all checks (greenlit), items with provenance gaps (flagged), and items the model declined to claim (skipped).
- Operator can approve all, edit inline, or block any item. If no items are flagged and the operator has enabled auto-send, the queue dispatches at 5:30am MT.
- Provenance log records: claim text, source URL, fetched-at timestamp, model version, prompt hash. Stored for 13 months.

## Constraints

- Delivery target: inbox by 6:00am MT for all Colorado subscribers.
- Dispatch latency: 50,000-subscriber list must complete handoff to the ESP within 35 minutes of send-start.
- Personalization cost ceiling: AI inference under $0.04 per subscriber per send, all-in.
- Generation language: Claude API for content drafting; provenance fields are required on every claim.
- No reader account required to subscribe; email address is the identity.

## Non-goals

- No reader-facing web app.
- No public web archive of past issues as a product surface; a basic archive page may exist for link-from-email continuity but is not promoted, not designed for retention, and not in the success metric.
- No paid reader tiers in v1. Funding model is sponsorship plus an optional super-fan tip jar.
- No multi-state expansion inside this product. The concept expands by spawning sister-newsletters per state, each with its own identity, list, and sending domain.
- No social features. No comments, no replies-as-content, no community surface.
- No behavioral tracking beyond opens and link clicks. No pixel chains, no fingerprinting, no third-party analytics in the email body.

## Success signal

- 35% or higher 30-day rolling open rate after the warm-up period.
- Under 0.1% spam complaint rate sustained.
- Under 0.5% unsubscribe rate per send sustained.
- At least one unsolicited reader reply per 1,000 subscribers per week describing a real-world action taken from the email (drew a tag, caught the ice-off, hit the wildflower window). This is the signal that the craft is working.

## Open questions

- Which ESP for v1: Postmark or Resend? Decision rides on deliverability track record at 50K daily and on dedicated-IP terms.
- How aggressive should the hallucination check be on the in-season recommendation, which is inherently judgment-laden (timing windows are estimates, not facts)? Need a different provenance bar for "fact" vs "call".
- Should the operator review queue include a one-keystroke "ship it" affordance, or force a quick visual diff against yesterday's send to catch drift?
- Sponsorship inventory: one slot per email or two? One protects the reading experience; two might be necessary for unit economics.
- Re-engagement send copy: do we ask "still want this?" or do we lead with a best-of? Test in week 8 of warm-up.
- Provenance retention: 13 months chosen for seasonal recurrence. Revisit at 12 months in production.
- Geographic resolution for sunrise / sunset: per-area or per-subscriber primary? Per-area is more accurate but costs more rows in the personalization step.
