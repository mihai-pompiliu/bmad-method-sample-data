# PomodoroLite — PRD

**Author:** me (solo)
**Date:** 2026-05-17
**Status:** draft, weekend build

## Problem

I already use the Pomodoro technique. Every iOS timer app I try wants to become a project manager. They have teams, tags, streaks, analytics dashboards, integrations with Notion and Slack, gamified XP bars, and a paywall in front of "focus sounds." I just want a 25-minute timer that respects my attention.

Existing options fail in one of two ways:
1. Bloated — six taps to start a timer, onboarding flows, account creation.
2. Toy — free timers that are buggy, ad-ridden, or abandoned.

There is room for an intentionally austere paid app. $4.99 one-time. No subscription. No account.

## Goals

- One tap from app open to running timer.
- Zero configuration required for the default Pomodoro loop (25 / 5 / 25 / 5 / 25 / 5 / 25 / 15).
- Works fully offline. No network calls, ever.
- Fits on one screen. No tabs, no menus, no settings drawer behind a hamburger.
- I can build the v1 in two weekends.

## Users

Solo knowledge workers who already know what a Pomodoro is. Writers, devs, researchers, students. They have tried the bloated apps and bounced. They want something that disappears.

Not for: teams, managers, people who want to track productivity over time, people who need to be sold on the technique.

## Features

- **FR-1: Start/stop timer.** Single large circular button center-screen. Tap to start a 25-minute work interval. Tap again to pause. Long-press to reset.
- **FR-2: Auto-advance through the Pomodoro loop.** After work interval ends, automatically start the break interval. After fourth work interval, take a 15-minute long break. Loop continues until user stops it.
- **FR-3: Audible + haptic notification on interval boundaries.** A single soft chime and a haptic tap. No persistent notification. No badge.
- **FR-4: Background execution.** Timer continues accurately when app is backgrounded or screen is locked. Use local notifications scheduled at interval end times.
- **FR-5: Adjustable interval lengths.** Tap-and-hold the duration label to edit work/short-break/long-break lengths. Persists locally. That is the only setting.

## Non-Functional

- Native SwiftUI. iOS 17+.
- No analytics SDK. No crash reporter that phones home. (I will eat the lost telemetry.)
- App size under 5 MB.
- Launch to interactive in under 400ms on iPhone 13 and newer.

## Success Metric

If 500 people pay $4.99 in the first six months and the one-star reviews are about taste rather than bugs, it worked.

## Open Questions

- Do I want a dark mode toggle, or just respect the system setting? Leaning toward system-only.
- Should the chime be customizable from a small bundled set, or just one chime? Leaning one chime.
- App icon — I will pay the graphic designer in trade.
