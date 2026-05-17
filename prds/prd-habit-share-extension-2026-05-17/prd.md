# Streakling: Shared Streak Mode

Status: draft, design-docs folder, talking to myself
Date: 2026-05-17

## Context

Streakling is the tiny iOS habit tracker I shipped two years ago. You pick up to 5 daily habits, check them off, watch a chain grow. Local-only by default, optional iCloud sync for your own devices. No account. No friends. No feed. The whole pitch on the App Store is "for you, not for them," and that line is the reason it has ~3,200 active users instead of zero (mostly word-of-mouth among friends, plus that one tweet that did numbers in early 2025).

People keep emailing me about one thing. Couples doing morning meditation together. Two siblings on a walking streak. A pair of friends quitting soda. They want to know the other person checked in. They are not asking for a social network. They are asking for one extra dot on their existing chain.

## What I'm adding

A "shared streak" mode. You pick one of your habits, generate a short invite code, send it to one other person via whatever (text, signal, smoke signal). They enter the code in their app. From that point on, that one habit is mirrored: when you check it off, they see it. When they check it off, you see it. Two dots per day on that row, one for each of you.

That is it. One habit. Two people. No chat, no nudges, no "you missed yesterday" guilt push.

## Existing pieces this touches

- Habit detail screen: needs a small "pair up" affordance, and the row needs to render two dots instead of one when paired.
- Habit data model: each habit gets an optional `pairing_id` and `partner_display_name`.
- Settings screen: a "Shared habits" subsection to view active pairings and unpair.
- iCloud sync code: untouched. Pairing state lives server-side, not in iCloud.
- Onboarding: untouched. New users still get the same quiet first-run.

## New functional requirements

- FR1: User can convert any existing solo habit into a paired habit by generating a 6-character invite code. Code expires in 24 hours if unused.
- FR2: Second user redeems the code in their app and selects (or creates) the habit on their side that should mirror.
- FR3: When either user checks off the paired habit, the other user's app shows the partner's check (with a small distinct mark, not identical to their own).
- FR4: Either user can unpair at any time from settings. Unpair is instant on their device and propagates to the partner within next sync.
- FR5: Each user can have at most 3 paired habits active at once (gut-feel cap, prevents this becoming a network).
- FR6: Pairing requires a lightweight server identity (anonymous device-bound token, no email, no password). Solo features keep working without ever creating one.
- FR7: If the backend is unreachable, the local check-off still works. Partner state shows a "last synced" timestamp and gracefully degrades.

## New constraints

- Server-side state is new for this app. I have to actually run something. Picking the cheapest boring stack I can (likely Cloudflare Workers + D1, or Supabase if I get lazy). Budget ceiling: $30/month at current user count.
- Anonymous tokens only. No email collection. No analytics on pairing content. I do not want to know what habits people are sharing.
- Pair data is minimum viable: pair_id, two device tokens, habit nickname, daily check booleans for the last 60 days. Older data drops off.
- The existing app must continue to function fully without ever calling the server. Solo users should not see a single new UI element unless they tap into the habit detail.

## Non-goals

- No friends list. You cannot see "all the people you are paired with" as a social graph. Each pairing is its own thing.
- No groups of 3 or more. I thought about it for an afternoon and the moment you have 3 people you need conflict resolution, display order, "who is slacking" implications. The whole vibe dies. Two only.
- No feed, no timeline, no activity view.
- No comments, reactions, emoji, nudges, reminders sent by partner, "poke" mechanic, or anything that sends a notification from one user to another.
- No partner profile. The other person is a nickname you typed in. That is the entire identity layer.
- No public sharing, no screenshot template, no "share your streak to Instagram" button.
- No web app, no Android, no widget changes in this release.

## Am I sure about this

Honestly, no. Streakling works because it is a calm app. Every social feature I have ever used has, over time, gotten louder. The risk is real that adding any partner-visibility breaks the spell, and the second I ship this people start asking for the next thing (groups, then comments, then streaks-of-the-week leaderboards, and then I have built the thing I left my last job to get away from).

The reason I am doing it anyway: the request is consistent, it is narrow, and the people asking are existing users who clearly already get what the app is for. If I keep the surface area tiny and refuse the obvious follow-on asks in public (changelog, App Store description), I think it survives.

If I am wrong, see rollback below.

## Success signal

If 50 of my existing ~3,200 users opt into a pairing within the first month, and App Store reviews do not start mentioning "social" or "friends" in either direction, I keep it. If pairing adoption is under ~15 users in a month, it was not actually wanted and I pull it. If reviews start saying "this app is becoming Strava," I pull it regardless of adoption.

Secondary signal: I personally use it with my sister for the morning walk habit for 30 days without it annoying me.

## Open questions

- Where does the server live. Workers vs Supabase vs a tiny Hetzner box. Leaning Workers for cost and zero ops.
- What happens when someone deletes the app and reinstalls. New device token, dead pairing on the other end. Show partner a "your partner is gone" state, offer to unpair. Probably fine.
- Do I show the partner's nickname on the home screen row, or only inside habit detail. Lean toward only inside detail to keep the main screen unchanged.
- 24-hour invite expiry: too short. Maybe 7 days. Decide before TestFlight.
- Notification on partner check-in: default off, opt-in toggle, or never. Leaning never for v1.

## Rollback plan if this sucks

If signals say bail: ship a release that hides the pair-up entry point, leaves existing pairings read-only for 60 days, then drops the server. Existing solo data is untouched. iCloud sync was never modified. Worst case the app reverts to exactly what it was a release ago, the server bill goes to zero, and I write a short blog post about why I pulled it. That post is probably better marketing than the feature itself.
