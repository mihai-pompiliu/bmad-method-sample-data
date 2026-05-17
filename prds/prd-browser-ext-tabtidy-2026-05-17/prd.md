# TabTidy PRD

- Product: TabTidy
- Type: Browser extension (Manifest V3)
- Owner: Solo dev (with possible help from a designer friend)
- Date: 2026-05-17
- Status: Draft v0.1

## Problem

People who keep 40+ tabs open lose track of what is there. Browsers offer tab groups, but creating and maintaining them is manual. The tabs people are actively using get visually drowned out by the tabs they opened three days ago and forgot. Existing tab managers tend to either do too much (full session managers, accounts, dashboards) or require giving a third party broad permissions over browsing data.

The result is a familiar loop: open tabs to "deal with later," never deal with them, close the window in frustration, lose the context, repeat next week.

## Target user

Knowledge workers, researchers, and developers who treat tabs as a working memory surface. They want their current focus visible and their stale context out of the way, without exporting their browsing to a SaaS.

Secondary: privacy-conscious users who have already uninstalled three tab managers because they did not like the permissions or the telemetry.

## Functional requirements

- FR1: On install, scan open tabs in the current window and group them by registrable domain using Chrome's native tab groups.
- FR2: Maintain grouping as new tabs open. A new tab joins its domain group if one exists, otherwise creates one.
- FR3: Track last-interaction time per group. A group is "touched" when any tab in it becomes active or is created.
- FR4: After 20 minutes of no interaction, collapse the group. Configurable (10 / 20 / 60 min) in options.
- FR5: Each group surfaces a "Park for later" action (via popup, accessible from the toolbar icon). Parking saves the group's tab list (title + URL) to storage and closes the tabs.
- FR6: A "Parked" page (extension page, opened in a normal tab) lists all parked groups with timestamp, domain, and tab count. Each group can be restored (reopens tabs in a new group) or deleted.
- FR7: Parked list syncs across the user's signed-in browser profile via `storage.sync`. Falls back to `storage.local` if quota is exceeded.

## Permissions surface (MV3)

Permissions requested in `manifest.json`, with the justification we will submit to the Chrome Web Store:

- `tabs`: read tab `url`, `title`, `windowId`, and `active` state to group tabs and detect interaction. Required.
- `tabGroups`: create, update, collapse, and color Chrome's native tab groups. This is the core capability.
- `storage`: persist parked groups and user settings. Uses both `sync` (parked list, settings) and `local` (interaction timestamps, which are per-device).
- `alarms`: schedule the 20-minute idle check without keeping a service worker alive.

Host permissions: none requested at install. We do not need `<all_urls>` because favicon URLs come from the `tab.favIconUrl` field, which `tabs` already provides. If we later want to render richer parked-page previews, we will add an optional `optional_host_permissions` entry the user can grant after the fact, never at install.

Permissions explicitly NOT requested: `webRequest`, `webNavigation`, `cookies`, `history`, `bookmarks`, `downloads`, `scripting` (no content scripts), `identity`. If a reviewer or user asks "why does this extension want X," the answer for any of these is "it does not."

## UX surface

- Toolbar icon (browser action): badge shows count of parked groups. Click opens the popup.
- Popup: shows the current window's groups, last-touched time, and a "Park" button per group. Footer link opens the Parked page.
- Parked page: a full-tab extension page (`chrome-extension://.../parked.html`). Lists parked groups, restore / delete per group, simple text search.
- Options page: idle timeout (10 / 20 / 60), auto-grouping on / off, "include pinned tabs" toggle.
- No context menu entries in v1. No DevTools panel. No omnibox keyword.

## Browser support

- Chrome: primary target. Manifest V3, Chrome 116+ (for stable `tabGroups` API behavior).
- Edge: ships as-is via the Edge Add-ons store. Same codebase, same manifest. Smoke-tested only.
- Firefox: deferred to v1.1. Firefox supports MV3 and `tabs`, but its tab groups story is new and the API surface differs. Treated as a port, not a primary target.
- Safari: not in scope. Safari Web Extensions do not expose a `tabGroups` equivalent at parity with Chrome, and the core capability of the product collapses without it. Revisit when Apple ships the API or we decide to ship a Safari-specific UX (e.g., a sidebar list instead of native groups).

## Privacy posture

- No network calls. The extension has no remote endpoint, no analytics SDK, no error reporter.
- No tracking, no IDs, no cookies. We do not generate or store a user ID.
- All persisted data is the user's own tab metadata (title + URL + timestamps) and lives in browser-managed `storage.sync` and `storage.local`. The dev has no access to it.
- Source code published on GitHub under MIT. The store listing links to the exact commit that built the submitted zip.
- Privacy policy is one page and says the above in plain English.

## Constraints

- MV3 service workers can be killed at any time. All scheduling goes through `chrome.alarms`. State lives in `storage`, not in memory.
- `storage.sync` quotas: 100 KB total, 8 KB per item. Parked list is chunked per group, with a soft cap of 200 parked groups before we prompt the user to clean up.
- Chrome Web Store review can take 1-14 days. Plan releases accordingly.
- Solo dev bandwidth: roughly one evening per week. Scope reflects that.
- No build pipeline beyond `vite` + a manifest copy step. No framework on the parked page beyond vanilla TS and a small CSS file. Keep the shipped zip small enough that a reviewer can read it in an afternoon.
- Accessibility: popup and parked page must be keyboard navigable. No custom focus traps, no exotic ARIA. Native semantics where possible.

## Risks

- Chrome could change `tabGroups` API behavior between releases. We pin a minimum Chrome version and test on Canary monthly.
- A user with hundreds of parked groups could hit `storage.sync` quota. We surface a clear warning and offer export to JSON before that point.
- The 20-minute idle heuristic might feel aggressive for some users. The options page exists to make this tunable on day one rather than after the first wave of one-star reviews.

## Non-goals (v1)

- No telemetry of any kind, even anonymous.
- No web companion app. No server. No account. No login.
- No paid tier. Free, MIT source on GitHub. Trust is the distribution strategy.
- No integration with bookmark managers (Pinboard, Raindrop, Notion, etc.).
- No Safari support.
- No full session manager features (named sessions, cross-window restore, history search).
- No AI summarization of parked groups, even though it is tempting.

## Success signal

- 500 weekly active users within 90 days of launch, measured only by the Chrome Web Store's built-in install / active counts (we add no telemetry of our own).
- Median Web Store rating of 4.5+ across at least 25 reviews.
- Qualitative: at least 10 unsolicited GitHub issues or discussions, indicating real users care enough to file.

## Open questions

- Do we auto-group on install, or wait for the user to opt in from the popup? Auto feels magical but may surprise people with 80 tabs.
- Should parked groups expire? E.g., auto-delete after 90 days, or never?
- Pinned tabs: ignore entirely, or treat each as its own non-collapsing group?
- Edge case: a tab whose URL changes domains mid-session. Do we move it between groups, or leave it where it started?
- Naming: "TabTidy" is fine but generic. Worth a 30-minute trademark + store-search check before we ship.
