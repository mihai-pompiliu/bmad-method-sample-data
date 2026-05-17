# Mobile App v2 Kickoff

**Date:** 2026-05-17
**Time:** 1:00pm to 2:00pm PT
**Location:** Zoom (recording: https://zoom.example/rec/dawnlog-v2-kickoff)
**Scribe:** Priya

## Attendees

- Alex (PM lead)
- Sam (Design lead)
- Marcus (iOS engineer)
- Devi (Android engineer, starting Q3)
- Jordan (Head of Marketing)
- Riley (Founder)
- Priya (PM, scribe)

## Agenda

1. State of Dawnlog today (5min)
2. What is v2 (15min)
3. Platform question: rewrite, Android, or both (20min)
4. Constraints and capacity (10min)
5. Decisions and action items (10min)

## 1. State of Dawnlog today (5min)

- Alex: iOS only, 42k MAU, 4.6 stars, churn is 9% monthly on free tier.
- Jordan: 60% of inbound waitlist signups in the last quarter asked for Android.
- Marcus: codebase is 3.5 years old, Swift 4 era, no SwiftUI, hard to add features cleanly.
- Sam: visual language hasn't been touched since launch. Feels dated next to Day One, Stoic, Reflectly.
- Riley: we are not the biggest, we are the most intentional. v2 has to feel like that.

## 2. What is v2 (15min)

- Alex: framing v2 as the "second product" not a polish pass.
- Sam: proposes a refreshed design system, typography-forward, calmer palette.
- Marcus: wants prompt streaks reworked, current implementation blocks daily-write feature.
- Riley: voice journaling. Hold a button, talk, get a transcribed entry. Non-negotiable for v2.
- Jordan: shareable "year in review" recap card, image export, drives organic growth.
- Sam: weekly reflection view, surfaces past entries on this date.
- Marcus: end-to-end encryption for entries. Current app stores plaintext on device, iCloud backup is unencrypted.
- Devi (joined audio only): Android needs widgets day one or it won't compete.
- Alex: AI-generated prompts based on past entries. Opt-in.
- Riley: mood tagging, lightweight, three taps max.
- Sam: export to PDF and Markdown, users keep asking.
- Jordan: deep link from share sheet, capture entry from anywhere.
- Priya (scribe note): that is 11 candidate capabilities on the board.

## 3. Platform question (20min)

- Jordan: every week we delay Android we leave money on the table. 60% of the market.
- Marcus: the iOS app cannot carry voice journaling or E2E encryption without a real rewrite. Bolting on will break.
- Riley: I want both. iOS rewrite and Android launch. That is the bet.
- Alex: we cannot do both in parallel with current team. Two iOS, one Android starting Q3, one designer.
- Marcus: if Android ships first, iOS rewrite slips to 2027. iOS users get nothing for a year.
- Devi: Android-first means I scope to a smaller v2 feature set. Voice journaling and mood tags, skip AI prompts in v1.
- Jordan: smaller Android scope is fine if we can market "Dawnlog is on Android" as the headline.
- Sam: the design system has to land before either platform builds. Otherwise we ship two visual languages.
- Alex: proposes incremental iOS rewrite. No greenfield. Migrate screen by screen behind a feature flag.
- Marcus: incremental is slower but does not strand existing users. Acceptable.
- Riley: existing iOS users are the brand. We cannot break their streaks or lose their entries.
- Alex: migration of existing iOS user data is a hard requirement, not a nice-to-have.
- Sam: design budget is one designer (me) plus 8 hours/week of contract illustration. Not enough for two parallel platform launches if we also rebuild the design system.
- Jordan: marketing can hold the Android announcement until we have something real. Not forever.
- Riley: [need to revisit] whether voice journaling ships in Android v1 or waits. Devi and Marcus to spike.

## 4. Constraints and capacity (10min)

- Alex: engineering capacity Q3: 2 iOS (Marcus + open req), 1 Android (Devi). No backend hire until Q4.
- Marcus: backend work for E2E encryption is non-trivial. Needs a backend engineer or we descope.
- Sam: design budget assumes design system work consumes June entirely.
- Alex: existing iOS users on v1 must keep working through the entire transition. No forced upgrades.
- Jordan: marketing needs 6 weeks lead time before any platform launch.
- Riley: runway is 14 months. v2 has to ship something meaningful inside 9.

## 5. Decisions

- Shared design system in Figma is the source of truth. Sam owns. Done by end of June.
- iOS rewrite is incremental, screen by screen behind feature flags. No greenfield. Scoped to v2.0 launch milestones, not a single big-bang release.
- Android discovery (technical spike + scoped feature list) completes by July 15. Devi + Alex.
- Existing iOS user data migration is a hard requirement. No streak loss, no entry loss.
- Voice journaling is in v2 scope. Platform sequencing TBD.
- E2E encryption is in v2 scope but gated on backend hire. If no hire by August, descope to on-device encryption only.
- AI-generated prompts are opt-in, not default.
- Marketing holds public Android announcement until August 1 at earliest.

## Action Items

| Owner | Action | Due |
|-------|--------|-----|
| Sam | Ship design system v1 in Figma | 2026-06-30 |
| Devi | Android technical spike + feature scope doc | 2026-07-15 |
| Alex | Draft v2 PRD covering shared capabilities | 2026-06-07 |
| Marcus | Spike on incremental iOS rewrite approach, feature flag infra | 2026-06-14 |
| Marcus + Devi | Joint spike on voice journaling feasibility per platform | 2026-06-21 |
| Alex | Hiring plan for backend engineer, present to Riley | 2026-05-31 |
| Jordan | Draft Android announcement plan, hold until launch criteria met | 2026-07-01 |
| Priya | Migration requirements doc for existing iOS users | 2026-06-07 |
| Riley | Decide voice-journaling-on-Android-v1 question after spike | 2026-06-28 |

## Parking Lot

- watchOS companion app
- Mac Catalyst port
- Subscription tier restructure (current pricing predates v2 scope)
- Web app for entry review
- Couples/shared journals
- Apple Intelligence integration
- Localization beyond English
- Analytics platform migration

## Next Meeting

- 2026-05-24, 1:00pm PT. Same room.
- Topic: review design system direction (Sam), review v2 PRD outline (Alex).
- Riley out, will review async (2pm. Alex had to drop at 1:55, missed close).
