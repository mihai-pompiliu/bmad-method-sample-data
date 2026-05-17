# PromptDeck PRD

**Product:** PromptDeck
**Platform:** macOS 14+ (Sonoma and later), Universal binary
**Owner:** Brian Madison
**Date:** 2026-05-17
**Status:** Draft v1, pre-implementation
**Distribution target:** Direct download, notarized + stapled DMG

## Problem

Power users of LLMs accumulate dozens of high-value prompts (refactor-this, summarize-this-thread, rewrite-in-my-voice, explain-this-error). Today those prompts live in scattered places: Notes, a Raycast snippet, a sticky note, a half-forgotten Obsidian file, the back of a ChatGPT history. Switching apps to run them breaks flow. Pasting context in by hand is friction. There is no Mac-native, keyboard-first way to keep a curated prompt library one hotkey away, with the active selection or clipboard auto-piped in, routed to the model you actually want for that job.

A Stream Deck device solves a related problem for OBS streamers and macro junkies, but it requires hardware, a config app, and a desk surface. Prompts are textual, contextual, and ephemeral. They belong on the keyboard, in a palette, next to the work, not on a USB-powered grid of LCD buttons.

## Target user

Mac-resident knowledge workers and developers who already pay for at least one LLM (Claude, GPT, or run Ollama locally), who live in the keyboard, who have a Raycast or Alfred muscle memory, and who have built up a personal collection of prompts they want at their fingertips. They know what notarization is. They have opinions about menubar app etiquette. They will notice if the palette flicks the active app out of focus. They will not notice, and will not care, that we use AppKit under the hood, but they will care if the result window is janky.

Not first-time AI users. Not teams (yet). Not Windows users. Not people who want a chat UI (there are eleven of those already).

The user we are not building for: enterprise IT buyer, AI-curious normie, prompt-marketplace browser, mobile-first user. We will lose all four of those segments cheerfully.

## Functional requirements

1. **Prompt library.** User defines named prompt buttons. Each button has: name, multi-line prompt body, optional system prompt, target model, optional variables.
2. **Variables.** Prompts can reference `{{clipboard}}`, `{{selection}}` (text selected in the frontmost app), `{{frontmost_app}}`, `{{frontmost_window_title}}`, `{{date}}`, and user-defined static variables.
3. **Hotkey palette.** Global hotkey (default Cmd+Shift+backtick, fully rebindable) summons a heads-up palette that lists prompts, supports fuzzy filter, arrow-key nav, return to fire.
4. **Model routing.** Per-prompt model selection across: Claude (Anthropic API), GPT (OpenAI API), local Ollama, AWS Bedrock. Keys stored in Keychain, never in plaintext on disk.
5. **Result window.** Streaming Markdown render in a floating, always-on-top-optional window with: copy-to-clipboard, paste-into-frontmost-app, save-as-note (to a user-chosen folder, plain .md).
6. **Library management.** Add, edit, duplicate, delete prompts. Tag prompts. Import/export library as JSON.
7. **History.** Last 50 runs are kept locally (timestamp, prompt name, model, response). Clearable. Local-only, no telemetry. Stored as plain JSON in Application Support so power users can grep it.
8. **Onboarding.** First-launch flow: pick a model provider, paste an API key (or point at local Ollama), import a starter prompt pack (5 prompts: refactor, summarize, rewrite, explain-error, brainstorm), grant Accessibility, fire a test prompt against the clipboard. Five steps. No accounts. No sign-up.
9. **Settings.** Native macOS Settings window (NSWindow with the SwiftUI Settings scene). Sections: General, Hotkeys, Models, Library, Advanced. Standard Cmd+, opens it.

## Mac-specific surface

- **Menubar resident.** Lives as a menubar item (NSStatusItem). No Dock icon by default. `LSUIElement = YES` in Info.plist. Optional preference to show in Dock for users who want it.
- **Global hotkey.** Implemented via the HotKey Swift package (Carbon under the hood). Requires Accessibility permission so the hotkey works while another app is frontmost. We explain this in onboarding, in plain language, and link to the System Settings pane directly.
- **Selection capture.** "Grab selected text from any app" requires Accessibility permission (we send a synthetic Cmd+C, read the pasteboard, restore the prior pasteboard). We tell the user exactly what we are doing and why. No silent permission grabs.
- **Notification Center.** Long-running prompts (greater than 8 seconds) post a UNUserNotificationCenter notification on completion if the palette has been dismissed.
- **Dock behavior.** When the result window is open, the Dock icon may appear transiently (NSApp.setActivationPolicy(.regular)) so Cmd+Tab works naturally, then drops back to accessory mode on close.
- **No Screen Recording permission.** Explicit non-goal. We do not capture the screen. If a future feature needs it, it will be opt-in and clearly disclosed.
- **AppKit + SwiftUI.** Menubar shell and result window in AppKit (more control over window level, activation, focus). Settings and library editor in SwiftUI hosted inside an NSWindow. Use `NSPanel` (non-activating, .floating level) for the palette so it can float above full-screen apps without stealing focus from the prior frontmost app.
- **Focus restoration.** When the palette closes, focus returns to the previously frontmost app. Paste-to-frontmost uses the recorded prior app, not whatever happens to be on top after the user dismissed the result window.
- **Appearance.** Respects system Light/Dark mode. Honors Reduce Motion (no palette spring animation when set). Honors Increase Contrast. Uses SF Symbols throughout. No custom chrome.
- **Multi-display.** Palette opens on the screen containing the cursor, not always the main display. Remembers result-window position per display configuration.
- **Spaces and full-screen.** Palette appears on the current Space, including over full-screen apps, via `NSWindow.collectionBehavior = [.canJoinAllSpaces, .fullScreenAuxiliary]`.
- **Login item.** Optional "launch at login" via SMAppService (the modern API, not the deprecated LaunchServices route). Off by default. On by default after first successful prompt run, if the user opts in during onboarding.
- **Menubar item behavior.** Left-click opens the palette (same as the hotkey). Right-click opens a small NSMenu with: recent prompts, Open Library, Settings, Pause Hotkey, Quit. Cmd-drag to rearrange position in the menubar. Hidden by Bartender? We test for that and offer a "show me even when hidden" hint.
- **Icon design.** Template image (PDF) so the menubar icon adapts to light/dark menubar tints automatically. Distinguishable at 16pt. No color, no badges, no notification dots in the menubar icon itself (that is what Notification Center is for).

## Distribution

**v1: Direct download only.** Notarized and stapled DMG, signed with a Developer ID Application certificate, distributed from the product site. Gatekeeper-clean on first launch (no right-click-open hack). Hardened runtime on. notarytool submission gated in CI before release.

Trade-offs considered:

- **Direct download (chosen for v1).** Pros: Accessibility-permission features work without sandbox fights. Faster ship. Full control over update cadence (Sparkle framework). Cons: User has to right-click-open the first time on some configs, has to grant Accessibility manually, no MAS discovery.
- **Mac App Store (deferred).** Pros: Trust, auto-update, payment rails. Cons: App Sandbox blocks the core feature (reading selection from arbitrary frontmost apps via synthetic keystrokes is incompatible with the sandbox entitlements available to third parties). Not worth crippling the product to land on MAS in v1.
- **Both (deferred).** A sandboxed "PromptDeck Lite" MAS build with only clipboard variables (no selection capture) is an option for v1.1+, not v1.

Updates via Sparkle 2 (EdDSA signed appcast). Crash reports opt-in only. Release cadence target: every 2-3 weeks for the first quarter, then monthly.

Homebrew cask published from day one (`brew install --cask promptdeck`). Setapp considered for v1.1 once the product has a track record; not at launch (revenue share is steep and the audience is not quite right).

## System integration

- **Shortcuts.app.** Ship App Intents: "Run PromptDeck prompt by name" (takes prompt name + optional input string, returns response string). Lets users chain PromptDeck into broader Shortcuts workflows (clipboard manager, automation, Stream Deck plugin, etc).
- **URL scheme.** `promptdeck://run?name=<prompt-name>&input=<text>` for triggering from Raycast, Alfred, Keyboard Maestro, or anything that can open a URL.
- **AppleScript.** Minimal scripting dictionary: `run prompt "name" with input "..."`. Returns the model response. Same surface as the URL scheme, exposed to the AppleScript world.
- **Spotlight.** Each saved prompt is indexed via Core Spotlight (CSSearchableItem) so typing the prompt name in Spotlight surfaces it. Selecting the result opens PromptDeck with that prompt staged.
- **Services menu.** Register a "Run with PromptDeck" Service so any app with selected text can right-click to fire a prompt. Each saved prompt can optionally expose itself as its own Service entry (so "Run Refactor Prompt" appears directly in the Services submenu).
- **Raycast bridge.** Ship a tiny Raycast extension (separate repo) that calls the URL scheme. Raycast users get our library inside their launcher with zero extra work. We do not require Raycast.
- **Dock menu.** When the user has opted to show the Dock icon, right-clicking it lists the 5 most-recently-used prompts as quick-launch items.

## Constraints

- **OS floor:** macOS 14 (Sonoma). No back-deployment.
- **Architecture:** Universal binary, Apple Silicon native. x86_64 slice for Intel users on Sonoma.
- **Code signing:** Developer ID Application cert, hardened runtime enabled, notarized via notarytool, stapled before DMG.
- **Sandboxing:** Not sandboxed in v1 (would break selection capture). Hardened runtime entitlements limited to what we actually use: `com.apple.security.automation.apple-events`, network client.
- **Permissions requested:** Accessibility (explained), Notifications (on first long prompt), Automation (for AppleScript targets if user enables paste-to-frontmost). No Full Disk Access. No Screen Recording. No Camera. No Mic.
- **Size budget:** App bundle under 25 MB.
- **Memory budget:** Idle resident memory under 80 MB. This app is always running. It must be a good citizen.
- **Performance:** Cold-launch palette response under 60ms from hotkey press to palette visible. Streaming first-token latency dominated by network, but UI must never block.
- **Keys:** All API keys in Keychain, per-account, per-provider. Never in UserDefaults, never in plist, never logged. Crash logs scrub any string matching a key pattern before submission.
- **Privacy:** No analytics by default. Optional, opt-in, locally-aggregated weekly summary that the user reviews and submits manually (or doesn't). No third-party SDKs. No fingerprinting. The app phones home for one thing: Sparkle appcast check, daily, configurable.
- **Offline behavior:** Local Ollama prompts work fully offline. Cloud-model prompts fail fast with a clear "you are offline" error and a "try local Ollama instead" suggestion if a local model is configured.
- **Concurrency:** Up to 3 in-flight prompts (different prompts, different result windows). A fourth queues. Streaming responses are not blocked by the UI thread, ever.
- **Localization:** English-only at v1. String table built for i18n from day one so a community translation pass is cheap later.
- **Accessibility (the other kind).** VoiceOver labels on every control. Palette is fully keyboard-navigable (it must be). Result window text is selectable and exposed to assistive tech. Dynamic Type respected within reasonable bounds in the result window.
- **Logging.** os_log subsystem `com.bmadcode.promptdeck`, categories: hotkey, model, palette, perms. User can pull a sysdiagnose-style bundle from Settings > Advanced for bug reports. No content of prompts or responses in logs.

## Non-goals

- Stream Deck (the physical device) support. v1 is keyboard-only. Hardware integration is a deliberate follow-up.
- Windows or Linux version. Mac-only. The whole pitch is "Mac-native."
- Training, fine-tuning, or hosting models locally. We talk to Ollama, we do not run training.
- Sharing prompts between users, team libraries, sync. Personal use only in v1. iCloud sync of the library is a v1.1 candidate, not v1.
- A prompt marketplace or prompt pack store. See open questions.
- A web version. There is no web version. There will never be a web version.
- A chat UI. PromptDeck is one-shot prompts with optional follow-up. If you want a chat, the existing app is right there.
- An agent framework. PromptDeck calls a model and shows the response. It does not loop, plan, or take actions on your machine beyond paste-to-frontmost.
- Built-in vector store, RAG over your files, or "talk to your notes." Out of scope. A future hook for piping in content from other tools is plausible, but not in v1.

## Success signal

Within 90 days of launch:

- 1,000 paying users at a one-time license price (target: 29 USD), or
- A measurable cohort (greater than 30 percent of week-1 installers) who are still firing greater than 5 prompts per day in week 4.

Either signal validates "Mac users want a keyboard-first prompt launcher and will pay for one." Both signals together would justify hiring help for v1.1.

Secondary qualitative signals: indie Mac review coverage (MacStories, Six Colors, Daring Fireball link), three or more Raycast/Alfred power-user blog posts, organic GitHub stars on the Shortcuts + URL scheme docs repo.

## Open questions

1. **Prompt pack marketplace.** Is there a v2 business in curated prompt packs (developer pack, writer pack, lawyer pack) sold through the app? Or does that violate the "personal tool" feel?
2. **iCloud sync vs. plain folder sync.** CloudKit is the Mac-native answer. A plain "watch this folder" model (point it at iCloud Drive, Dropbox, or a git repo) is simpler and lets power users version-control their prompts. Pick one for v1.1.
3. **Sparkle vs. in-app updater.** Sparkle is the standard, but a custom updater gives us better UX for the "Accessibility permission was revoked after update" edge case. Probably Sparkle, confirm.
4. **Free tier shape.** Free with a cap (5 prompts in library)? Free for 14 days then paid? Free forever, paid for cloud-model routing? Need to pick before launch.
5. **Bedrock auth.** AWS SSO flows are painful in a notarized non-sandboxed app. Static IAM keys are easier but worse security. Decide before shipping Bedrock support, or defer Bedrock to v1.1.
6. **MAS Lite build.** Is the marketing value of "available on the Mac App Store" worth maintaining a second, crippled build? Revisit after v1 ships.
7. **Stream Deck device support.** Eventual v2 hardware path. Do we ship as a Stream Deck plugin (Elgato's plugin SDK), or as a companion that the Stream Deck calls via our URL scheme? The second path is much cheaper to build and ship.
8. **Licensing system.** Paddle, Gumroad, or self-hosted (Stripe + a tiny license server)? Self-hosted gives the best margins and the worst weekend. Paddle handles VAT.
9. **Naming.** "PromptDeck" is currently a working title. Trademark check pending. Backup name: "Keystroke."
10. **Variable model.** Should variables support transformation (e.g., `{{clipboard | trim | first 500}}`)? Powerful, but a slippery slope toward "we built a template language." Probably yes for v1.1, no for v1.
11. **Conflict resolution for hotkeys.** Cmd+Shift+backtick is rare but not unused. We detect the conflict on first launch (best-effort) and prompt the user to rebind. Is that enough, or should we ship with an unbound default and force the user to pick?
