---
id: SPEC-rss-revival
created: 2026-05-17
updated: 2026-05-17
status: draft
source_artifact: null
---

# Spec: RSS Revival

## Problem

People who left Twitter/X are looking for a calmer way to follow writers, blogs, and publications without an algorithmic feed deciding what they see. Existing RSS readers feel dated, demand accounts, or push subscriptions and cloud services the user does not want. A solo developer with weekend time wants to ship a native iOS reader (with an optional Mac build via Catalyst) that respects the user's attention, stores data locally, and syncs quietly through iCloud.

## Capabilities

### CAP-1: Subscribe to a feed by URL
- intent: A reader can add a new source by pasting an RSS or Atom URL and confirming the feed.
- success: Pasting a valid feed URL results in the feed appearing in the sidebar with its title and latest items within a few seconds.

### CAP-2: See unread items grouped by feed
- intent: A reader can scan what is new across all subscriptions, organized by source rather than by a single merged timeline.
- success: The main view lists each feed with its unread count, and selecting a feed shows only that feed's unread items in reverse chronological order.

### CAP-3: Read full article inline
- intent: A reader can read the full text of an article inside the app without being bounced to Safari for every link.

### CAP-4: Import an OPML file from another reader
- intent: Implement OPML import using a JSON-based parser with async fetch, parsing the OPML into intermediate JSON and then iterating the array to insert each feed row.
- success: Selecting an OPML file from Files results in every valid feed in that file being subscribed, with a summary of how many succeeded and failed.

### CAP-5: Mark items read or save for later
- intent: A reader can clear items they have seen and set aside items they want to return to.
- success: Items marked read disappear from unread views, and saved items appear in a dedicated "Saved" list that persists across launches.

### CAP-6: Sync read state across devices via iCloud
- intent: A reader using the app on iPhone and Mac sees the same read and saved state on both devices without creating an account.
- success: An item marked read on one device shows as read on the other device within a minute, using the user's existing iCloud account and no app-specific login.

## Constraints

- Native iOS app, with Mac support via Mac Catalyst from the same codebase.
- No accounts, no app-operated servers, no third-party sync service. All sync uses CloudKit under the user's iCloud account.
- Local-first storage: the app must remain fully functional with no network and no iCloud.
- Must be high-quality software.
- Built solo on weekends, so scope must fit a small surface area maintainable by one developer.

## Non-goals

- No social features (no sharing graphs, no follower counts, no comments).
- No algorithmic ranking, recommendation engine, or "for you" feed.
- No Android or web client in the initial release.
- No paid subscription tier at launch; a one-time App Store purchase only.

## Success signal

- 500 downloads in the first month after App Store release.
- Users find it refreshing.
