---
title: PomodoroLite — Product Brief
status: pre-PRD
created: 2026-05-17
---

# PomodoroLite

**Tagline:** A Pomodoro timer that disappears.

## Problem

Every Pomodoro app on the App Store wants to be more than a timer. They add teams, tags, streaks, dashboards, integrations, gamified XP bars, paywalled focus sounds, account creation, and onboarding carousels. The thing I actually wanted, a 25-minute timer that respects my attention, has been buried under feature creep. The free alternatives are either buggy, ad-ridden, or abandoned. There is no calm, paid middle.

## Target user

Solo knowledge workers who already practice the Pomodoro technique. Writers, developers, researchers, grad students. They have tried the bloated apps, felt the friction, and bounced. They want a tool that gets out of the way.

Not for: teams, managers, productivity hobbyists who want analytics, or anyone who needs to be sold on the technique itself.

## Vision

PomodoroLite feels like picking up a kitchen timer. You open it, you press the thing, you go to work. When you come back twenty-five minutes later it has done exactly what you expected and nothing else. There is no dashboard waiting for you, no streak to protect, no nudge to upgrade.

The product's job is to be boring on purpose. Every design decision should be a subtraction. If a feature does not survive the question "would a kitchen timer do this?", it probably should not ship. The aesthetic should feel closer to a well-made physical object than to a SaaS app.

## Key capabilities

- Start a focus interval in a single tap from a cold launch.
- Run the standard Pomodoro loop end to end without configuration.
- Signal interval transitions gently, without commandeering the user's attention.
- Keep accurate time when the app is backgrounded or the device is locked.
- Allow the user to adjust interval lengths if they want to, without making them.
- Work fully offline, with no account and no network dependency.
- Stay on a single screen. No tabs, no menus, no settings buried behind a hamburger.

## Why now

The "calm tech" and one-time-purchase indie iOS niche has quietly come back. Users are visibly fatigued by subscription fatigue and AI-everything feature bloat. There is a small but real willingness to pay a few dollars once for software that respects them. A weekend-scale build can ship into that gap before someone louder fills it.

## Constraints and assumptions

- Solo build. Scope must fit in roughly two weekends.
- Native iOS only for v1. No Android, no web, no Mac Catalyst.
- One-time purchase at a low single-digit price. No subscription, no IAP, no ads.
- No telemetry, no analytics SDKs, no crash reporters that phone home. Lost insight is acceptable cost.
- iOS-current only. Not going to support old OS versions for a v1.

## Success signals

- A few hundred paid downloads in the first six months with no marketing spend beyond a launch post.
- One-star reviews, when they appear, are about taste (too minimal, ugly icon) rather than bugs.
- I personally still use it as my daily timer three months after shipping.

## Open questions

- Light/dark mode: respect system, or offer a manual toggle?
- Chime sound: one curated chime, or a small bundled set the user can pick from?
- How much customization of interval lengths is healthy before it stops feeling like a kitchen timer?
- Is there any version of "history" (even just "today's count") that fits the ethos, or does any history at all break it?
- App icon and visual identity. I do not have a designer lined up yet.
