# PRD: CommitNudge (VS Code Extension)

- Status: Draft
- Owner: Eng-PM, Developer Experience
- Date: 2026-05-17
- Target surface: VS Code (1.90+), Cursor, VSCodium
- Distribution: VS Code Marketplace + Open VSX

## Problem

Engineers on our team routinely lose work, stall PRs, or end up with branches that take a full afternoon to untangle because they did not commit at a sane checkpoint. The failure mode is not "I forgot Git exists." It is "I was in flow, and now it has been six hours, and the diff is no longer reviewable." Existing tooling either yells (modal popups), is invasive (auto-commits with generated messages), or is invisible (Git status bar shows ahead/behind, not staleness).

We want a light-touch signal that lives in the editor and degrades to background noise when the developer is genuinely fine. The right design is closer to a smoke detector than a phone notification: present, ignorable, only loud when it has to be.

Three concrete pain patterns from internal interviews:

1. The "Friday afternoon untangle": one branch, 1,200 lines, eight loosely related changes, now needs to be split across four PRs.
2. The "lost laptop": real incident, real lost day of work, no commits between 9am and 5pm.
3. The "passing tests, then not": tests were green at 2pm, the dev kept editing, by 4pm something broke and the last known-good state is unrecoverable without `git stash` gymnastics.

## Target user

Individual contributor engineers using VS Code with Git. Solo or small-team workflows. Comfortable with the CLI, but spend most of the day inside the editor. Not interested in another panel, another login, another dashboard.

Out of scope as users: engineering managers, release managers, anyone wanting team-level aggregation.

## Functional requirements

- FR1: Detect "uncommitted work pressure" using three signals computed locally:
  - Lines added or changed since `HEAD` exceed a threshold (default 200).
  - Hours on the current branch since last commit exceed a threshold (default 24 working hours, naive 9-to-6 weekday clock).
  - Tests last ran green more than 20 minutes ago and the working tree is still dirty. (Detected via watching common test-runner tasks; v1 watches `tasks.json` task completion events only.)
- FR2: Surface the current nudge state as a single status bar item: green dot (calm), amber dot (one threshold crossed), red dot (two or more crossed, or any single threshold crossed by 2x).
- FR3: Clicking the status bar item opens a small webview panel with one line per active signal explaining why the dot is amber or red, plus a button that runs `git add -p` in the integrated terminal.
- FR4: Provide a tree view inside the existing Source Control panel labeled "Since last commit" with three rows: files changed, lines changed, hours on branch.
- FR5: Recompute on file save, on branch change, and on a 60-second idle timer. Never on every keystroke.
- FR6: Never write to the repo. No `git commit`, no `git add` without explicit user click. No file modifications.

## Extension surface (VS Code APIs)

- `vscode.window.createStatusBarItem(StatusBarAlignment.Left, priority: 50)`: the dot. Low priority so it sits to the right of the Git branch indicator.
- `vscode.commands.registerCommand('commitnudge.showWhy', ...)`: the only command. Bound to the status bar item's `command` field. Also surfaced in the command palette as "CommitNudge: Show why I'm nudging".
- `vscode.window.createWebviewPanel(...)`: the explanation panel. Static HTML, no external network, CSP locked down.
- `vscode.window.createTreeView('commitnudge.sinceLastCommit', { ... })` contributed to the existing `scm` view container via `package.json` `contributes.views.scm`. We do not create a new view container.
- `vscode.tasks.onDidEndTaskProcess`: catch test runs.
- `vscode.workspace.onDidSaveTextDocument` and `git` extension API (`vscode.extensions.getExtension('vscode.git').exports`) for branch and HEAD diff state.
- `contributes.walkthroughs`: a single first-install walkthrough with three steps (what the dot means, where the tree view lives, how to silence it).
- No code lens. No decorations. No notifications API. No modal dialogs.

## Settings surface

Workspace settings (`.vscode/settings.json`):

- `commitnudge.thresholds.linesChanged` (number, default 200)
- `commitnudge.thresholds.hoursOnBranch` (number, default 24)
- `commitnudge.thresholds.minutesSinceGreenTests` (number, default 20)
- `commitnudge.workingHours.start` (string `HH:mm`, default `09:00`)
- `commitnudge.workingHours.end` (string `HH:mm`, default `18:00`)

User settings (per machine):

- `commitnudge.quietMode` (boolean, default `false`): hides the status bar item entirely. The tree view in SCM stays. No other UI.
- `commitnudge.testTaskNamePatterns` (array of strings, default `["test", "vitest", "jest", "pytest", "go test"]`): substrings matched against task labels to identify "the test run."

All settings respect the `resource` and `window` scopes documented in the configuration contribution point.

## Telemetry posture

- No telemetry. The extension does not import `@vscode/extension-telemetry` and does not call any network endpoint.
- The marketplace `package.json` declares `"capabilities": { "untrustedWorkspaces": { "supported": "limited" } }` and the README contains an explicit "No telemetry, no network calls, no analytics" section, per Marketplace guidance.
- The extension respects `telemetry.telemetryLevel` defensively (returns early in any code path that might log), even though no logging exists, so future contributors do not regress this by accident.

## Constraints

- Activation event: `onStartupFinished` only. Not `*`. The extension must not contribute to cold-start time perceived by the user.
- Activation cost budget: under 50ms wall time on a 2021 MacBook Pro M1 baseline, measured via `code --prof-startup`.
- Steady-state memory budget: under 25MB resident in the extension host.
- No blocking calls in the extension host. All `git` invocations go through the bundled Git extension API or `child_process` with timeouts (default 1500ms).
- Disk reads on the 60-second timer must short-circuit if the workspace has no `.git` directory.
- No bundled binaries. Pure TypeScript, no native modules. Total VSIX size under 500KB.

## Non-goals

- Not generating commit messages with AI. Not now, not as a follow-on. The product position is "the developer decides what to commit."
- Not auto-committing under any condition.
- Not adding a separate activity bar icon or sidebar view container. The SCM panel is the only home.
- Not supporting non-Git VCS (Mercurial, Pijul, Jujutsu, Fossil) in v1. Git only, detected via the bundled `vscode.git` extension.
- Not adding team-aware features. No aggregation, no shared thresholds, no Slack/Discord webhooks, no leaderboards.
- Not integrating with PR tools (GitHub PRs, GitLab MR extension, Azure Repos) in v1.
- Not shipping a settings UI beyond what `contributes.configuration` gives us for free.

## Success signal

- 70% of installs that survive the first week still have the status bar item visible (i.e., quiet mode adoption stays under 30%). Measured via Marketplace install/uninstall counts and one optional, opt-in survey at day 14, not via in-extension telemetry.
- Internal team dogfood: median branch age at merge drops by at least 25% over a four-week window, measured from Git history, not from the extension.
- Zero P1 bugs related to extension host stalls, repo corruption, or unintended Git state changes (we should never cause these, because we never write).

## Risks

- Risk: the dot becomes wallpaper. Devs stop seeing it. Mitigation: the red state subtly pulses (CSS animation on the status bar item background, opacity 0.6 to 1.0, 2s ease). No flashing, no color cycling, no motion that would trip accessibility flags.
- Risk: false positives during refactors that legitimately span many lines (renaming a symbol across the codebase). Mitigation: the threshold is per-developer-configurable, and the "why" panel always lists which signal fired so the dev can dismiss intelligently.
- Risk: extension host slowdown from git diff polling. Mitigation: cache the diff stat result for 60 seconds; invalidate on save or branch change only.

## Open questions

- Should the 20-minute "tests went green and you still have not committed" signal also fire for terminal-launched test runs we cannot observe? Today we only see tasks. Possible v1.1: parse common test-runner output via a pseudo-terminal wrapper, behind a setting.
- Working hours definition: do we need per-day overrides for engineers on non-standard schedules, or is one start/end pair acceptable for v1? Leaning toward one pair.
- Should the tree view show a fourth row for "commits ahead of remote" or is that redundant with the built-in Git status bar? Leaning redundant, leaving out.
- Webview vs. quick pick for the "why" surface: webview gives us the `git add -p` launcher button cleanly, but quick pick activates faster. Pending a perf test on the M1 baseline.
- Open VSX parity: do we ship day-one to Open VSX for Cursor and VSCodium users, or wait for v1.1? Leaning day-one, the build cost is near zero.
