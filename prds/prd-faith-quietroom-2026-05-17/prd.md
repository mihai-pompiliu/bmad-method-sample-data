# QuietRoom PRD

**Status:** Draft v0.1
**Owner:** Founding PM (writes from a personal prayer practice; not claiming theological authority)
**Date:** 2026-05-17
**One-line:** A private prayer log, with an optional small room of people you actually know.

## Vision

Most prayer apps end up looking like social networks for piety. Public feeds, follower counts, prayer "streaks," AI-generated devotionals on autoplay. That is the opposite of what prayer is for most people who actually pray.

QuietRoom is a small, quiet tool. It helps a person keep track of what they are praying for, who they said they would pray for, and (if they choose) it lets a handful of people they trust hold those intentions together. That is the whole thing.

We are not trying to be the platform for faith. We are trying to be a respectful, durable tool that disappears into a practice the user already has.

## Target User

We are building for people with an active prayer or contemplative practice. Specifically:

- Christians across traditions (Protestant evangelical, mainline Protestant, Catholic, Orthodox), including people who pray the Daily Office or the Rosary and people who pray extemporaneously
- Observant and reform Jewish users, including chevrutah pairs and small communities outside formal minyan
- Muslim users, with the awareness that salah is structured and time-bound; QuietRoom is for du'a and intention-keeping, not a replacement for prayer itself
- Buddhist practitioners, with the awareness that "prayer" is not a clean translation; the app supports intention, dedication of merit, and metta-style practice without imposing theistic framing
- Twelve-step members keeping a sponsor-circle prayer list
- Spiritually serious non-affiliated practitioners who pray or sit with intention regularly
- Small-group leaders: church small-group facilitators, women's circle leaders, men's group leaders, recovery sponsors, family prayer leaders

We are not building for the curious, the casual, or the audience-seeking. They are welcome. They are not who we design for.

## Capability Surface

Personal log
- Prayer intentions: short title, optional detail, dated, status of praying / answered / released
- "Praying for" list: people, situations, things, with an optional last-prayed-for timestamp
- Answered-prayer log, kept separately, for gratitude and reflection
- Optional Scripture or passage reference attached to an intention (Bible, Tanakh, Qur'an, sutra, or user-defined source)
- Memorial: when a person you have prayed for passes, a quiet way to mark the transition and keep the entry

Reminders and rhythm
- Configurable reminder times, off by default
- Practice-aware defaults: a Muslim user's morning reminder will not be placed during Fajr; liturgical users can map reminders to canonical hours; Jewish users can align with evening
- Optional prayer-time integration: adhan times for Muslim users, canonical hours for liturgical traditions, candle-lighting times where relevant. All optional, all off by default.

Small Room (the only social feature)
- Invite-only group of 4 to 12 people the user knows personally
- Members can share specific intentions into the room; nothing is shared automatically
- "I prayed for this" acknowledgment: a tap. No count is shown to anyone. The sharer sees that acknowledgments happened, not a leaderboard.
- Room goes dormant after 90 days of inactivity and requires a member to reactivate it

Optional, off by default
- Gentle descriptive summary of one's own log over time (e.g., "you have been praying for your father's health since January"). Descriptive only, never interpretive, never devotional.
- Tradition-specific daily reading, available on demand, never auto-attached to an intention

## Community Model (Load-bearing)

This is the decision the product rests on. We will be tempted to soften it. We will not.

- Small Room only. Never larger than 12.
- No global feed. No public profile. No follower count. No discovery.
- Invitation by SMS, email, or QR code only. No directory.
- No cross-room mechanics. A user in three rooms sees three rooms. They do not see "people praying near you" or "popular intentions."
- Optional prayer-partners: a within-room pairing for an explicit reciprocal commitment between two members.
- Inactive rooms shut down at 90 days and must be reactivated by a member.

If we ever build a public surface, this PRD is wrong and should be rewritten. Not amended.

## Theological and Practice Considerations

- The product is interfaith-respectful, not interfaith-flattening. Defaults adjust to the user's stated tradition. The app does not present a generic spiritual aesthetic that satisfies no one.
- Language adjusts to tradition where the user opts in. "Answered" is offered as one status; users in traditions where that framing is uncomfortable can rename it (e.g., "received," "completed," "released").
- The app does not generate prayers. This is a line. We do not produce AI prayers, AI devotionals, AI scripture interpretation, or AI spiritual direction. The user prays. The app keeps the record.
- Scripture and passage references are surfaced from configured text sources. We do not paraphrase, condense, or "explain" sacred text.
- We will consult with practitioners from each named tradition during design review, and credit them only with their permission.

## Privacy Posture

Prayer content is among the most personal data a person produces. We treat it that way.

- End-to-end encryption on prayer content. The user's passphrase derives the encryption key.
- Small Room content is encrypted with a shared room key. The company cannot read it. Support cannot read it.
- Support tooling cannot search a user's prayer log. Engineering acknowledges this is a real operational constraint and accepts it.
- No analytics on prayer content. We can know that a user opened the app. We cannot know what they wrote.
- Account deletion is immediate and complete. No soft-delete window, no archival.
- No third-party trackers. No advertising SDKs. Ever.

## Constraints

- Free tier: full personal log, one Small Room, local-only storage
- Pro tier: $4/month or $36/year. Multiple Small Rooms, longer history, optional E2E cloud sync across devices.
- iOS and Android at launch. No web client at launch (we do not want a browser surface for this data yet.)
- Localized at launch: English, Spanish, Arabic (RTL), Hebrew (RTL)
- No advertising, in any form, ever
- No selling, sharing, or licensing user data. Stated prominently, not buried.

## Non-Goals

Listed explicitly so the product does not drift.

- Not a global prayer wall or social feed
- Not a church management product (Planning Center exists)
- Not a Bible-study or scripture-reading app (YouVersion, Sefaria, Quran.com exist; we link, we do not replace)
- Not a meditation app (Calm, Insight Timer exist)
- Not a religious content publishing platform
- Not generating prayers, devotionals, or spiritual instruction with AI
- Not building prayer streaks, badges, levels, or any gamification
- Not a marketplace for spiritual directors or clergy
- Not a livestream platform for services

## Success Signal

We are looking for evidence of practice, not engagement.

- Users adding intentions or acknowledging prayer said, weekly, sustained over months. Not session count.
- Small Room health: rooms with 4 or more members where at least 3 are active weekly.
- Retention through difficult periods. Qualitative: users report that the practice held during hard months.
- Trust: zero data-related incidents. Reviews repeatedly mention privacy and respect for tradition, unprompted.
- Negative signals we will watch for: rooms maxing out at 12 and members asking for more, requests for public sharing, requests for AI-generated content. Each of these is feedback we are drifting.

## Open Questions

- How do we handle a Small Room member who dies? The room needs a graceful protocol; the family may or may not be in the room.
- Memorial entries: keep forever, or offer the user a way to gently retire them after a self-chosen period?
- Should small-group leaders get any leader-specific surface, or does that violate the "no hierarchy" principle of the room? Current bias: no special role.
- Pricing in non-USD markets: $4/month is too high in some launch regions. Need regional pricing without making it feel like a tier system or like spiritual practice is paywalled.
- Reminder defaults for Buddhist users: framing reminders as "prayer" may itself be wrong. Consult before shipping defaults.
- Do we offer a clergy or spiritual-director mode, or is that a different product? Current answer: different product. Revisit only on direct request from practicing clergy.
- What is the policy if law enforcement requests data? We believe the encryption posture makes us unable to comply with content requests. We need this reviewed by counsel before launch, and stated plainly in the privacy page.
- Recovery use case: do AA-style sponsor circles want anonymity within the room itself? Current model assumes members know each other by name. Some recovery contexts may want first-name-only or handle-only display.
- Children and minors: do we permit accounts under 18 at all? If so, how do parents enter the picture without breaking the privacy posture?
