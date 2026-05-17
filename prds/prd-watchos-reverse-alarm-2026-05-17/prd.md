# WindDown PRD

**Product:** WindDown, a reverse alarm clock for Apple Watch
**Platform:** watchOS 10+ (paired iOS 17+ companion)
**Status:** Draft
**Date:** 2026-05-17
**Owner:** Brian Madison

## Problem

Alarms wake us up. Nothing tells us to stop. Phones in bed are the silent thief of sleep duration. Existing "bedtime" features live inside iOS notification center, which is the exact surface they are trying to protect us from. The nudge to put the phone down should not come from the phone.

The wrist is the right surface for this. It is glance-first, haptic-native, and physically attached to the user in the moments that matter (the last hour before sleep, when the phone is in hand and attention is gone).

## Target user

Adults, 25-45, who already use Apple Watch daily and already care about sleep enough to have set a Bedtime in Health. They have tried screen-time limits and bounced off them. They want gentle, ambient cues. Not enforcement, not gamification, not another dashboard.

## Functional requirements

FR-1. User sets a target sleep time (HH:MM) and a target duration (hours).
FR-2. Watch app fires three haptic pulses on the wrist: 45 min out (gentle), 30 min out (medium), 15 min out (firm).
FR-3. Each pulse surfaces an actionable prompt with three choices: "yes, set DND", "snooze 15", "skip tonight".
FR-4. "Yes" triggers a user-configured Shortcut (set on the iPhone companion). Default Shortcut: enable Do Not Disturb until target wake time.
FR-5. Complications (corner, circular, rectangular) show tonight's wind-down time. Tap launches the app to the main page.
FR-6. Morning summary view: last 7 nights, which pulses the user said yes to, snoozed, skipped. No score. No streak.
FR-7. App must work standalone on cellular Watch SKUs after initial setup.

All user-facing strings must fit watch text constraints. No paragraphs. Buttons are verbs. Times are HH:MM. No tutorial walls of text on the watch.

## Watch surface

**Main page (root view).** Single screen: "Tonight's wind-down at HH:MM" in large type. Below it, a small status row: next pulse in 1h 12m. That is the entire main page.

**Settings page.** Long-press the main page to reveal: target sleep time, target duration, pulse pattern (default / minimal / firm), and a toggle for "use Shortcut on yes". No deeper navigation tree.

**Prompt sheet.** Triggered by each pulse. Three vertically-stacked buttons. Crown rotation dismisses.

**Complication families.**
- Corner: "WD 22:30" (label + time).
- Circular: clock-face glyph with target time below.
- Rectangular: "Wind-down 22:30, next pulse 21:45".

**Haptics.**
- 45-min: `WKHapticType.notification`. Soft. A tap, not an alarm.
- 30-min: `WKHapticType.directionUp`. A nudge with shape.
- 15-min: `WKHapticType.success`. Firm and final. Counter-intuitive in name, correct in feel (single decisive thump, no buzz-trail).

**Crown vs. swipe.** Crown dismisses prompts and scrolls the 7-day summary. Swipe is reserved for system gestures only. No custom swipe handlers.

## Companion iOS app surface

The iPhone app is setup and review only. It is not the nudge layer.

- Initial setup wizard (target sleep time, duration, pulse pattern).
- Shortcuts picker for the "yes, going to bed" action. Default Shortcut auto-installed on first launch.
- Weekly summary chart (Swift Charts), one screen, scrollable by week.
- Permissions handoff (notifications, Shortcuts, HealthKit read for sleep window display only).
- No push notifications from the phone. The watch is the only nudge surface.

## Constraints

- Must run standalone on cellular Watch SKUs after first-launch pairing. No iPhone presence required for nightly operation.
- Battery: less than 2% drain per day on Series 9 and later. Use background refresh sparingly. Schedule pulses via `WKExtendedRuntimeSession` or local notifications, not polling.
- Cold-launch performance: app interactive within 800ms on tap from a complication. No async network on launch.
- Sync: settings sync via WatchConnectivity when both devices are awake. Watch holds the source of truth at sleep time.
- No server. No account. No analytics SDK. Local-only state, encrypted at rest by the OS.

## Non-goals

- Not tracking sleep. HealthKit already does this and does it well.
- Not enforcing. No app blocking, no scolding copy, no streak shame, no red numbers.
- Not building a phone-side notification system. If the user wants a phone nudge, they can build a Shortcut.
- Not integrating with mattresses, rings, beds, or third-party sleep hardware.
- Not social. No leaderboard, no share sheet, no friends, no community.
- Not a habit tracker. The 7-day view is review, not accountability.

## Success signal

Primary signal: percentage of users who say "yes, set DND" on the 15-minute pulse, week over week. If users are accepting the final pulse, the product is working. Target: 40% acceptance by week 4 of use.

Secondary signal: 30-day retention above 35%. Apple Design Award submission cycle 2027.

Anti-signal: high snooze rate (over 60%) means the times are wrong or the user is not actually ready to sleep. We surface this back to the user as a gentle suggestion to adjust target time. We do not surface it as a failure.

## Open questions

- Should the Shortcut be triggered from the watch directly (watchOS 10 supports this) or handed off to the phone? Watch-direct is cleaner but limits Shortcut complexity.
- Do we offer a "weekend mode" with shifted times, or is that scope creep into habit-tracker territory?
- Smart Stack relevance: should the widget bubble up in the Smart Stack during the wind-down window? Probably yes, but needs design.
- Always-On Display behavior during the wind-down hour: do we dim or do we surface the countdown? Lean dim, but worth a prototype.
- Naming: "WindDown" is the working title. Trademark check pending.
