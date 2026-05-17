---
title: Heart Rate Monitor Portal
created: 2026-05-15
updated: 2026-05-15
---

# PRD: Heart Rate Monitor Portal

## 0. Document Purpose

This PRD specifies the Heart Rate Monitor Portal, a web application that lets a doctor review heart rate data for their assigned patients whose monitors are actively transmitting. It is the source of truth for downstream UX, architecture, and story work in this project.

The PRD is structured around features grouped behaviorally with FRs nested under each, a Glossary that fixes domain vocabulary, and named user journeys that downstream artifacts reference by ID. Assumptions, when present, appear inline as `[ASSUMPTION: ...]` and are indexed in §12. This is a portfolio project; the product is specified as if real, but data is sourced from a fake API and no production deployment or regulated-domain compliance work is in scope.

## 1. Vision

Doctors managing patients on remote heart rate monitors today juggle scattered tools — device portals, patient charts, separate alert systems — to answer one recurring question: *which of my patients needs attention right now?* The Heart Rate Monitor Portal collapses that into a single view. A doctor logs in, sees only their assigned patients whose monitors are actively transmitting, scans status at a glance, and drills into any patient for live readings, historical trends, and threshold alerts.

The portal is built primarily for **periodic review** — the doctor opens it a few times a day, triages who needs attention, takes action, and closes it. **Active monitoring** is a secondary mode for a doctor who wants to keep a patient's detail view open and watch readings update live. The product is not a monitoring station; it is a triage and review surface.

It exists to shorten the loop between "something is happening to a patient" and "the doctor sees it" — without adding another always-on screen to a doctor's day.

## 2. Target User

### 2.1 Primary Persona

**Dr. Lena Park, outpatient cardiologist.** Lena runs a busy clinic and follows a panel of patients on home heart rate monitors — some recovering from cardiac events, some on rhythm management, some being worked up for arrhythmias. Between appointments she opens the portal to check on the patients she is actively managing. She is comfortable with software but does not want to live inside it; the portal earns its place by making each visit short and useful.

Lena has two scopes of patients:

- **Active panel (10–30 patients):** the people she is currently watching closely. This is her default view.
- **Supervisory panel (up to several hundred):** patients on monitors under her broader supervision — colleagues' patients she covers, longer-term follow-ups, and others she no longer actively manages but remains responsible for. She reaches into this pool occasionally, usually with a specific patient in mind.

### 2.2 Jobs To Be Done

- Triage in under a minute: see which of my active patients need attention right now.
- Drill into one patient and understand their recent heart rate picture without hunting across tools.
- Know which alerts I have already seen and which are new since I last checked.
- Trust the data: know which monitors are actually transmitting and which have gone quiet.
- Reach a specific supervised patient on demand without losing my active-panel context.
- Keep a patient open and watch live readings when something looks off, without that being the default mode.

### 2.3 Non-Users (v1)

- **Patients.** No patient-facing view, account, or data entry. Patients interact with their device, not this portal.
- **Nurses, MAs, care coordinators.** Single-role product in v1; team workflows are out of scope.
- **Administrators / billing / IT.** No assignment management, audit reporting, or device provisioning UI.

### 2.4 Key User Journeys

- **UJ-1. Lena's between-patients triage.**
  Lena finishes a clinic appointment and has six minutes before the next one. She opens the portal on her workstation, already authenticated from earlier. Her Active Panel loads as the default — a list of her 22 currently-watched patients, sorted so anything flagged sits at the top. Two patients show new Alerts since her last check; one shows a Monitor that has gone quiet for 4 hours. She opens the first flagged patient, sees a 24-hour Trend with the Alert moment highlighted, scans recent Readings, and recognizes it as expected post-exertion elevation. She acknowledges the Alert and returns to the panel. She does the same for the second. The quiet Monitor she notes mentally — she will call the patient later. She closes the tab. Total time: under three minutes.
  - **Climax:** seeing "two new since last check" plus the quiet-Monitor flag tells her in one glance whether her panel needs her right now.

- **UJ-2. Lena watches a patient live while she finishes something else.**
  A patient on her Active Panel throws an Alert that does not match the usual pattern. Lena opens the patient's Detail View and switches it to Live Mode. Readings update every few seconds in the corner of her screen while she works in another system. She watches for ten minutes, sees Readings stabilize, decides not to escalate, acknowledges the Alert with a short note, and closes the view.
  - **Climax:** the Live Mode view confirms stabilization in real time without manual refresh.

- **UJ-3. Lena pulls a supervised patient on demand.**
  A partner calls about a patient under Lena's Supervisory Panel — not on her Active Panel. She searches by patient name from anywhere in the app, lands on the patient's Detail View (clearly marked as supervisory), reviews the last 48 hours of Readings and Alerts, answers her partner's question, and closes the tab. Her Active Panel is unchanged when she returns.
  - **Climax:** she gets to the answer in under 30 seconds without losing her Active Panel state.

## 3. Glossary

- **Doctor** — The portal's sole user role in v1. A cardiologist responsible for one or more Patients. Identified by a doctor account.
- **Patient** — A person assigned to a Doctor and wearing a Monitor. Patients do not access the portal directly.
- **Monitor** — The remote heart rate device worn by a Patient. One Patient has one Monitor at a time.
- **Remote-Enabled** — Status of a Monitor transmitting Readings to the back end (a Reading has arrived within the Freshness Window). Mutually exclusive with Quiet and Never-Transmitted.
- **Quiet Monitor** — Status of a Monitor that was Remote-Enabled previously but has stopped transmitting beyond the Freshness Window. Surfaced as a distinct status from "no alert."
- **Never-Transmitted Monitor** — Status of a Monitor that has never produced a Reading. Distinguished from Quiet because there is no last-known transmission timestamp.
- **Reading** — A single heart rate data point from a Monitor, timestamped. The fundamental unit of data in the portal. The back end discards Readings with bpm ≤ 0 or otherwise outside a plausible range before they reach the portal.
- **Trend** — A time-windowed sequence of Readings rendered visually (e.g., 24-hour, 7-day). Trends are derived from Readings; the portal does not store separate Trend records.
- **Threshold** — A configured boundary on Readings (upper/lower bpm) that, when crossed, opens an Excursion. v1 Thresholds are system-wide defaults (see §3.1); per-Patient Threshold configuration is out of v1 and deferred.
- **Excursion** — A continuous run of Readings outside Threshold bounds. Opens at the first Reading where `reading >= upper` (or `<= lower`); ends at the first Reading where `reading < upper` (or `> lower`). The Threshold value itself is *out* of the band. One Threshold Alert is generated per Excursion.
- **Alert** — A signal generated when an Excursion opens, or when a Monitor becomes Quiet. Has a state: *new*, *acknowledged*. Carries the moment of generation and (for Threshold Alerts) the Reading that opened the Excursion.
- **Acknowledgment** — A Doctor's action on an Alert marking it seen. Optionally carries a free-text Note. Acknowledged Alerts remain visible in history but no longer flag the Patient as needing attention.
- **Active Panel** — The Doctor's default view: the set of Patients they are actively managing. Typically 10–30 Patients; soft-capped at 60 (see §3.1). The back end (fake API) determines Patient inclusion in the Active Panel; the portal does not edit it in v1. A Patient is on either the Active Panel or the Supervisory Panel for a given Doctor, never both.
- **Supervisory Panel** — The broader set of Patients under the Doctor's supervision but not on the Active Panel. Reachable by search; not browsed as a list in v1.
- **Detail View** — The single-Patient screen showing recent Readings, Trends over selectable windows, Alert history, and Monitor status.
- **Live Mode** — A state of the Detail View in which Readings update in near real time. Secondary mode; the Detail View opens in static (most-recent-snapshot) mode by default.
- **Last-Check Marker** — A server-recorded timestamp, set at sign-in to the *end of the Doctor's prior session* (explicit sign-out time, or the timestamp of the last user-initiated activity if the session expired). Live Mode auto-fetches do not advance the marker. Used to compute "new since last check" indicators on the Active Panel. On a first-ever sign-in, the marker is set to the sign-in time itself so historical Alerts are not bulk-flagged as new.
- **Freshness Window** — The maximum time since a Monitor's last received Reading before it transitions to Quiet status. See §3.1.
- **Doctor Session** — A Doctor's authenticated portal context. Sessions are singleton per Doctor: a new sign-in invalidates any prior active session (across browsers or devices).

### 3.1 System-wide Defaults (single source of truth)

The PRD references a set of system-wide constants throughout. This table is the single source of truth; FRs and NFRs reference these values rather than restating them:

| Constant | Value | Used in |
|---|---|---|
| Freshness Window | 10 minutes | Quiet Monitor detection (FR-5, FR-18), Detail View stale-Reading indicator (FR-13) |
| Lower bpm Threshold | 40 bpm | Threshold Alert generation (FR-17). Cardiology sanity-check pending (§11). |
| Upper bpm Threshold | 120 bpm | Threshold Alert generation (FR-17). Cardiology sanity-check pending (§11). |
| Quiet Alert Dedup Window | 15 minutes | Minimum interval between consecutive Quiet Alerts on the same Patient (FR-18). |
| Active Panel Soft Cap | 60 Patients | Performance NFRs (§8) guaranteed up to this size; >60 not v1-supported. |
| Active Panel Refresh Cadence | 30 seconds | Auto-refresh while open (FR-4). |
| Alert History Cap | 100 most recent per Patient | Displayed in Detail View Alert history (FR-20). Older unacknowledged Alerts remain reachable. |
| Note Length Cap | 500 grapheme clusters | Acknowledgment Note (FR-19). |
| Session Inactivity Timeout | 8 hours | Doctor Session expiry (FR-2). |
| Live Mode Update Bound | 5 seconds | Maximum staleness from back-end Reading arrival to render (FR-16). |

## 4. Features

### 4.1 Authentication & Session

**Description:** The Doctor signs in to the portal, lands on their Active Panel, and remains authenticated across page reloads within a Doctor Session. Session and Last-Check Marker semantics are defined in the Glossary (§3) and tested in FR-2 / FR-3. Authorization and server-authoritative comparison are governed by §8 Security. Realizes the entry state of UJ-1, UJ-2, UJ-3.

**Functional Requirements:**

#### FR-1: Sign in

A Doctor can sign in with credentials and reach their Active Panel as the post-login landing surface. Realizes UJ-1.

**Consequences (testable):**
- Successful sign-in routes to the Active Panel, not a generic dashboard.
- Failed sign-in shows a non-leaky error (no enumeration of valid vs. invalid usernames).
- An authenticated user navigating directly to a non-existent URL is shown a not-found state, not bounced to login.

#### FR-2: Session persistence and singleton enforcement

A Doctor's Session persists across page reloads and tab navigation within the portal until explicit sign-out or expiry. Sessions are singleton per Doctor across browsers and devices (Glossary §3, Doctor Session). Realizes UJ-2 (live mode left open across context switches).

**Consequences (testable):**
- A reload of any portal page within the active Session keeps the Doctor signed in.
- Sessions expire after the configured Session Inactivity Timeout (§3.1).
- A new sign-in for the same Doctor invalidates any prior active session; the prior session's tabs route to sign-in on next request.
- Sign-out clears session state and routes to sign-in.

#### FR-3: Last-Check Marker

The system records a per-Doctor Last-Check Marker representing the end of the Doctor's previous Session, available to other features for "new since last check" calculations. Realizes UJ-1.

**Consequences (testable):**
- On the first-ever sign-in for a Doctor, the Last-Check Marker is set to the current sign-in time; the Active Panel does not bulk-flag historical Alerts as new.
- On subsequent sign-ins, the marker is set to the end of the prior Session: the explicit sign-out timestamp, or the timestamp of the last user-initiated authenticated request if the prior Session expired by inactivity.
- Live Mode auto-fetches do not count as user-initiated activity for the purpose of advancing the marker.
- The marker is set only at sign-in, not continuously during a Session — so "new since last check" remains stable across a single Session.
- "New since last check" comparisons are performed server-side against server-recorded Alert and marker timestamps; the client never participates in the comparison.

### 4.2 Active Panel

**Description:** The Active Panel is the Doctor's default landing view: a list of the 10–30 Patients they are actively managing, each row exposing enough status at a glance that the Doctor can decide in seconds whether the Patient needs attention. Rows are sorted so anything needing attention sits at the top. "New since last check" markers distinguish Alerts the Doctor has not yet seen this Session from older ones. Clicking a row opens the Patient Detail View. Realizes UJ-1.

**Functional Requirements:**

#### FR-4: View Active Panel

A Doctor can see their Active Panel as the default surface after sign-in, listing every assigned Patient on their Active Panel. The panel auto-refreshes on the cadence defined in §3.1, on mount, and on manual refresh. Realizes UJ-1.

**Consequences (testable):**
- All Patients returned by the back end as on the Doctor's Active Panel appear in the list, exactly once each.
- An empty Active Panel (no assigned Patients) shows an empty state, not an error.
- Panel size is rendered up to the Active Panel Soft Cap (§3.1); back ends returning more than the soft cap are not supported in v1, and the portal logs the condition.
- The panel auto-refreshes every Active Panel Refresh Cadence interval (§3.1) while the tab is in the foreground.
- A manual refresh action is available and refreshes the panel immediately.

#### FR-5: Patient row content

Each Patient row in the Active Panel shows: Patient name, primary status indicator, most recent Reading value, and time-since-last-Reading. Realizes UJ-1.

**Consequences (testable):**
- The primary status indicator resolves to exactly one of: *flagged* (one or more unacknowledged Alerts), *quiet* (Monitor is Quiet but no unacknowledged Alerts), or *normal*. *Flagged* is determined from all unacknowledged Alerts (not only those within the Alert History Cap visible in Detail View).
- Status is encoded by at least two channels: a color treatment AND a non-color carrier (shape, glyph, or text label). Status is never communicated by color alone.
- Most recent Reading and time-since-last-Reading reflect the latest data the back end has returned; the absence of any Reading within the Freshness Window transitions the row to *quiet*.
- A row with both unacknowledged Alerts and a Quiet Monitor renders as *flagged* with a secondary "monitor quiet" hint, not two competing primary states.
- Row identity is the Patient ID; a Patient name change between refreshes does not reset the row's "new" markers or sort position.

#### FR-6: Default sort order

Active Panel rows are sorted, top to bottom: *flagged* first, then *quiet*, then *normal*; within each group, most-recent-Alert (or most-recent-status-change) first; with Patient ID ascending as the final deterministic tiebreaker. Realizes UJ-1.

**Consequences (testable):**
- A newly-flagged Patient moves to the top of the panel on the next data refresh. The cross-group transition is ordered as if the transition itself were a status-change event at the transition timestamp.
- Acknowledging the last unacknowledged Alert on a *flagged* Patient drops them out of the *flagged* group on the next refresh.
- The sort is fully deterministic: identical underlying data produces identical row order, with Patient ID as the final tiebreaker.

#### FR-7: New-since-last-check row markers

Each row indicates whether it carries Alerts created after the Last-Check Marker. Realizes UJ-1.

**Consequences (testable):**
- A Patient with one or more Alerts created (server-side timestamp) after the Last-Check Marker shows a visually distinct "new" indicator.
- Acknowledging an Alert within the current Session does not remove the "new" indicator until the next sign-in. A *normal* row may carry a "new" indicator (meaning: there was new activity this Session, already handled).
- A Patient with no Alerts created after the marker shows no "new" indicator, regardless of older unacknowledged Alerts.
- The check is per-Alert created-time vs. marker, independent of whether the Patient was on the Active Panel at the prior session boundary.

#### FR-7b: Active Panel summary counts

The Active Panel renders, alongside the row list, two summary counts visible without scrolling: the count of Patients carrying Alerts created after the Last-Check Marker, and the count of Patients in *quiet* status. Realizes UJ-1.

**Consequences (testable):**
- Both counts are computed from the current panel data and refresh with the panel.
- A zero count renders as "0" (visible), not hidden, so the Doctor can confirm at a glance that there is nothing new.
- The counts use Glossary terms verbatim in their labels (e.g., "new since last check," "quiet monitors").

#### FR-8: Open Detail View from row

A Doctor can click any Active Panel row to open that Patient's Detail View. Realizes UJ-1.

**Consequences (testable):**
- Clicking a row routes to the Patient's Detail View at a stable, shareable URL.
- The browser back action returns to the Active Panel with the same sort and "new" markers as before (Active Panel state preserved).

### 4.3 Patient Search & Supervisory Access

**Description:** The Doctor can reach any Patient under their care — Active Panel or Supervisory Panel — by searching from anywhere in the app. Results clearly distinguish Active from Supervisory so the Doctor never mistakes a covered Patient for one on their primary panel. Opening a Supervisory Patient's Detail View does not alter the Doctor's Active Panel state; returning lands the Doctor exactly where they were. Realizes UJ-3.

**Functional Requirements:**

#### FR-9: Search Patients from anywhere

A Doctor can invoke search from any portal surface and query Patients by name. Realizes UJ-3.

**Consequences (testable):**
- A search affordance is reachable on the Active Panel and the Patient Detail View without page navigation.
- Submitting a query of two or more grapheme clusters returns matching Patients across both the Active Panel and the Supervisory Panel.
- Match semantics: case-insensitive, diacritic-insensitive substring match against the Patient's full name.
- A query that matches nothing shows an empty-results state, not an error.

#### FR-10: Distinguish Active vs. Supervisory in results

Each search result is visually labeled as *Active* or *Supervisory*. Realizes UJ-3.

**Consequences (testable):**
- Every result row carries exactly one of the two labels; results are never ambiguous (a Patient cannot be on both panels for the same Doctor — see §3 Glossary, Active Panel).
- Sorting within results puts *Active* matches above *Supervisory* matches when names tie.

#### FR-11: Open Supervisory Detail View

A Doctor can select a Supervisory result and open that Patient's Detail View, which is itself clearly marked as supervisory via a persistent banner above the Detail View header. Realizes UJ-3.

**Consequences (testable):**
- The Detail View of a Supervisory Patient shows a persistent banner immediately above the header identifying it as supervisory; the banner remains visible while scrolling and while in Live Mode.
- All Detail View capabilities (Readings, Trends, Alerts, Live Mode, Acknowledgment) are available for Supervisory Patients in v1. Acknowledgment behavior for Supervisory Alerts is defined in §4.5.

#### FR-12: Preserve Active Panel state across supervisory navigation

Opening a Supervisory Patient and returning leaves the Active Panel sort, scroll position, and "new" markers unchanged. Realizes UJ-3.

**Consequences (testable):**
- Returning from a Supervisory Detail View via browser back lands on the Active Panel with prior state intact.
- The Last-Check Marker does not advance because of a supervisory dive.

**Out of Scope:**
- Browsing the Supervisory Panel as a paged list. Supervisory access is search-only in v1.

### 4.4 Patient Detail View

**Description:** The Patient Detail View is the single-Patient screen where the Doctor reviews recent Readings, looks at Trends over selectable windows, sees Alert history, and checks Monitor status. The view opens in **static mode** by default — a snapshot of the most recent state — and can be switched to **Live Mode** for real-time updates while the Doctor watches. The static-first default keeps the periodic-review use case fast, and the Live Mode toggle keeps the active-monitoring use case available without forcing it. Realizes UJ-1 and UJ-2.

**Functional Requirements:**

#### FR-13: Detail View composition

A Detail View renders, for one Patient: a header (name, Monitor status chip), the most recent Reading with its timestamp, a Trend chart for a default window, and a chronological Alert history (most recent first). Supervisory Patients additionally render a persistent supervisory banner above the header (FR-11). Realizes UJ-1, UJ-2, UJ-3.

**Consequences (testable):**
- All four regions render on initial load. A Patient with no Alerts shows an empty Alert history region, not a missing region.
- Header Monitor status reflects current Remote-Enabled, Quiet, or Never-Transmitted state (Glossary §3).
- The most-recent Reading region is empty (with explanatory copy) when the Monitor is Never-Transmitted.
- When the most-recent Reading is older than the Freshness Window, the Reading is displayed with a visible "stale" treatment matching the Active Panel *quiet* affordance.

#### FR-14: Selectable Trend windows

A Doctor can switch the Trend chart between fixed time windows: 24-hour, 7-day, and 30-day. Realizes UJ-1.

**Consequences (testable):**
- Switching windows re-renders the chart without leaving the Detail View.
- The selected window persists for the duration of the current Detail View session but resets to default on a fresh Detail View open.
- Windows with no Readings render an empty-window state, not an error.

#### FR-15: Static mode default

A newly opened Detail View is in static mode: the visible state is a server-side snapshot at the time of open and does not auto-update. Realizes UJ-1.

**Consequences (testable):**
- Opening a Detail View triggers exactly one data fetch for that view; no polling occurs in static mode.
- A Doctor can refresh the Detail View manually and the snapshot updates to current.

#### FR-16: Live Mode toggle

A Doctor can toggle the Detail View into Live Mode, causing Readings, Monitor status, and Alert history to update in near real time; toggling back returns to static mode without losing the Doctor's place. Realizes UJ-2.

**Consequences (testable):**

*Happy path:*
- In Live Mode, the most-recent Reading region renders newly-arrived Readings within the Live Mode Update Bound (§3.1) of their arrival at the back end.
- New Alerts that arrive during Live Mode appear at the top of Alert history.
- Toggling out of Live Mode stops updates immediately and freezes the view at the last-rendered state until the Doctor refreshes or re-enters Live Mode.

*Lifecycle:*
- Browser navigation away from a Live Mode Detail View terminates Live Mode immediately; returning via browser back lands in static mode at the last-rendered snapshot.
- Page reload reopens the Detail View in static mode (consistent with FR-15); Live Mode state is not persisted across reload.
- When the Live Mode tab is closed, no further client-initiated requests for that Patient's data originate from this user's browser context until Live Mode is re-entered.

*Concurrency invariant (load-bearing for UJ-2):*
- **Acknowledgment dialog is sticky across history re-renders.** An open Acknowledgment dialog remains open through Live Mode updates; its target Alert ID is bound at dialog-open time; underlying list updates do not swap its target.

*Edge handling:*
- Live Mode toggle is available even when the Monitor has Never-Transmitted: the most-recent Reading region shows "waiting for first Reading" and begins rendering Readings as soon as the first arrives.
- On repeated Live Mode fetch failures (3 consecutive), the view shows a non-blocking "live updates paused — retrying" affordance; underlying data remains at the last good state; recovery clears the affordance.

**Feature-specific NFRs:**
- Live Mode is for a single open tab at a time; if the same Doctor opens Live Mode for the same Patient in a second tab, behavior is undefined but must not corrupt server state. Accepted v1 limitation.

### 4.5 Alerts & Acknowledgment

**Description:** Alerts are the portal's primary signal that a Patient needs attention. Two kinds, defined in FR-17 (Threshold Alerts) and FR-18 (Quiet Monitor Alerts). The Doctor sees Alerts surfaced in two places: as a flag on the Patient row in the Active Panel (FR-5), and in chronological Alert history in the Patient Detail View (FR-13). The Doctor acknowledges an Alert, optionally with a Note, marking it seen; acknowledged Alerts remain in history but no longer flag the Patient. Acknowledgment is available to any Doctor with access to the Patient — Active or Supervisory — and the acknowledgment record carries the acknowledging Doctor's identity, so coverage actions are attributable. Cross-cuts UJ-1, UJ-2, UJ-3.

**Functional Requirements:**

#### FR-17: Threshold Alert generation

The system generates a Threshold Alert when an Excursion opens against the v1 system-wide upper or lower bpm Thresholds (§3.1). Realizes UJ-1, UJ-2.

**Consequences (testable):**
- An Excursion opens at the first Reading where `reading >= upper` (or `<= lower`) and closes at the first Reading where `reading < upper` (or `> lower`). The Threshold value itself is *out* of the band.
- Exactly one Threshold Alert is generated per Excursion (no per-Reading Alert spam during a sustained Excursion).
- A first Reading that is already out-of-band — including the first Reading from a Never-Transmitted Monitor, or the first Reading after a Quiet period — opens an Excursion and generates an Alert. Unknown or empty prior state is treated as in-band for the crossing model.
- A Quiet period closes any open Excursion at the start of the Quiet. The first Reading after recovery is evaluated fresh.
- Threshold values used are the v1 system-wide defaults (§3.1); per-Patient overrides are not honored if present in the data.

#### FR-18: Quiet Monitor Alert generation

The system generates a Quiet Monitor Alert when a Patient's Monitor transitions from Remote-Enabled to Quiet (last received Reading older than the Freshness Window). Realizes UJ-1.

**Consequences (testable):**
- A Monitor that goes Quiet generates one Quiet Monitor Alert on the transition; resuming transmission does not generate a "monitor back" Alert in v1.
- A Never-Transmitted Monitor does not generate Quiet Monitor Alerts.
- Consecutive Quiet Monitor Alerts on the same Patient are separated by at least the Quiet Alert Dedup Window (§3.1); flapping Monitors do not produce one Alert per quiet period inside the dedup window.
- The Quiet Monitor Alert carries the timestamp of the last received Reading.
- If a Monitor resumes transmission while a prior Quiet Alert remains unacknowledged, the Alert remains in *new* state until the Doctor acknowledges it. The Detail View header updates to *Remote-Enabled* immediately, and the Active Panel row reflects the new Monitor status on the next refresh while the unacknowledged Alert still keeps the row *flagged*.

#### FR-19: Acknowledge Alert

A Doctor can acknowledge any unacknowledged Alert on a Patient currently on their Active Panel or Supervisory Panel, optionally attaching a free-text Note up to the Note Length Cap (§3.1). Realizes UJ-2.

**Consequences (testable):**
- Acknowledging an Alert sets its state to *acknowledged*, records the acknowledging Doctor's identity, the server-side timestamp, and the Note (if any).
- Acknowledging a Quiet Monitor Alert clears the *flagged* status on the Active Panel row; the row remains *quiet* as long as the Monitor remains beyond the Freshness Window. A new Quiet Monitor Alert is not generated for the same uninterrupted Quiet period.
- Note constraints: the client trims leading and trailing whitespace; embedded whitespace and newlines are preserved; an empty result is stored as "no Note"; the server enforces the length cap authoritatively in grapheme clusters.
- The server is the authority on idempotency: a re-submitted acknowledgment for an Alert ID already in *acknowledged* state returns the existing record without creating a duplicate or overwriting the original; the client UI updates to show the existing acknowledgment metadata. Notes submitted in a re-submission are discarded with a user-visible toast.
- The server re-checks panel membership at acknowledgment submission time; if the Patient is no longer on either of the Doctor's panels, the action is rejected and the client routes to a "this Patient is no longer in your care" state.

#### FR-20: Alert history visibility

Acknowledged Alerts remain visible in the Patient Detail View Alert history with their acknowledgment metadata (acknowledging Doctor, server timestamp, Note); they no longer flag the Patient row on the Active Panel. Realizes UJ-1, UJ-2.

**Consequences (testable):**
- A Patient with only acknowledged Alerts in their history renders as *normal* (or *quiet* if applicable) on the Active Panel, not *flagged*.
- Active Panel *flagged* status is computed from **all** unacknowledged Alerts for the Patient, not only those visible inside the Alert History Cap window.
- Alert history shows acknowledgment metadata inline with each acknowledged Alert.
- History is ordered most-recent first and renders up to the Alert History Cap (§3.1). If unacknowledged Alerts exist beyond the cap, an "older unacknowledged Alerts (N)" affordance is rendered at the bottom of the history allowing the Doctor to reach them.

**Notes:**
- Per-Patient Thresholds: deferred — see §6.2.

## 5. Non-Goals (Explicit)

- **Patient-facing surfaces.** No login, app, or self-service for Patients. The product is for Doctors only.
- **Team and multi-role workflows.** No nurses, MAs, care coordinators, or admins as users. No assignment management, audit reporting, or device provisioning surfaces.
- **Real device integration.** All data is sourced from a fake API; no manufacturer cloud connections, no real telemetry, no device pairing flows.
- **Regulated-domain compliance work.** HIPAA, GDPR, SOC 2, and similar postures are explicitly out of scope as a portfolio project on fake data. A production version would require this work; the PRD does not pretend otherwise.
- **EHR integration.** No reading from or writing to electronic health record systems.
- **Out-of-portal alerting.** No email, SMS, push, or pager alerting. Alerts surface only inside the portal.
- **Per-Patient configuration.** No per-Patient Thresholds, no per-Patient watchlist tuning, no per-Patient Live Mode preferences. System-wide defaults only in v1.
- **Mobile-native or tablet-optimized experiences.** Desktop web only (see §10 Platform).
- **Becoming a continuous monitoring station.** The portal supports active monitoring as a secondary mode; it is not a clinical-grade monitoring station and must not be positioned as one.

## 6. MVP Scope

### 6.1 In Scope

- Doctor sign-in, singleton session enforcement, and session continuity (§4.1).
- Active Panel as default landing surface, with status indicators, sort, summary counts, new-since-last-check markers, and a 30-second auto-refresh cadence (§4.2).
- Patient search from anywhere, spanning Active and Supervisory; Supervisory Detail View labeled with a persistent banner (§4.3).
- Patient Detail View with header, most-recent Reading (with stale treatment when applicable), Trend chart across 24h/7d/30d, Alert history with older-unacknowledged affordance, Monitor status (§4.4).
- Live Mode toggle inside Detail View, rendering new Readings within 5 seconds of back-end arrival (§4.4).
- Threshold Alerts on system-wide default upper/lower bpm with Excursion semantics, including first-Reading-out-of-band and Quiet-period boundary handling (§4.5).
- Quiet Monitor Alerts on Freshness-Window expiry, with 15-minute dedup between consecutive Quiet Alerts on the same Patient (§4.5).
- Alert acknowledgment with optional Note up to 500 grapheme clusters; Alert history with acknowledgment metadata; ack-of-Supervisory-Alerts with attribution (§4.5).

### 6.2 Out of Scope for MVP

- **Per-Patient Threshold configuration.** Deferred to v2; flagged as the most likely first capability extension. `[NOTE FOR PM]`
- **Patient assignment management inside the portal.** Active/Supervisory membership is read-only from the back end in v1.
- **Browsing the Supervisory Panel as a paged list.** Search-only access in v1; v2 may add list browsing if the use case proves out.
- **Out-of-portal alerting (email/SMS/push).** Deferred. Doctors must be in the portal to see Alerts.
- **Notes beyond Alert acknowledgment.** No free-form patient notes, no chart notes. Out of v1.
- **Alert history pagination beyond the Alert History Cap (§3.1).** The "older unacknowledged Alerts (N)" affordance (FR-20) reaches the unacknowledged tail; browsing the full historical Alert log is deferred.
- **Multi-tab Live Mode defined behavior.** v1 accepts that behavior is undefined across simultaneous Live Mode tabs.
- **Multi-device concurrent sessions.** v1 enforces singleton sessions per Doctor; a new sign-in invalidates any prior session. Concurrent sessions across devices are out of v1.
- **Mobile, tablet, or PWA targets.** Desktop web only in v1.
- **Real-device or EHR integration.** Not v1; not v2 in current planning.

## 7. Success Metrics

This product is a portfolio piece on fake data; the metrics below name two layers:

- **Portfolio-Reality SMs** judge whether *this artifact and its downstream chain* succeeded as a showcase.
- **Aspirational-Clinical SMs** name the metrics a real version would track if the portal ever shipped to actual cardiologists. Downstream architecture and stories can reference them as design pressure without pretending we will measure them.

### 7.1 Portfolio-Reality (judging the showcase)

**Primary**

- **SM-P1.** Full BMad chain produces a deployable build from this PRD: PRD → UX → architecture → epics → stories → working code. Target: every chain artifact is produced and the resulting build runs end-to-end on the fake API. Validates the artifact, not the product.
- **SM-P2.** A demo of UJ-1 (between-patients triage) runs in under 90 seconds, beginning from a pre-staged Doctor Session with a Last-Check Marker already recorded. Three of three viewers, without prompting, can correctly identify (a) which Patient is *flagged*, (b) why, and (c) what action Lena took. Validates FR-4, FR-5, FR-6, FR-7b, FR-8, FR-13. (FR-7 is excluded — its prior-session dependency cannot be exercised in a single live demo without the pre-staging precondition above.)

**Secondary**

- **SM-P3.** UJ-2 (Live Mode watch) and UJ-3 (Supervisory pull) are demonstrable on the same build without code changes. Target: both shown in one continuous walkthrough. Validates FR-9 through FR-12, FR-15, FR-16.

**Counter-metrics (do not optimize)**

- **SM-PC1.** *Lines of code.* Don't optimize for size of the codebase — a portfolio piece earns nothing from being big.
- **SM-PC2.** *Feature breadth beyond §6.1.* Don't add features to make the demo more impressive. The slice is the slice.

### 7.2 Aspirational-Clinical (if this were real)

**Primary**

- **SM-A1.** *Time-to-triage.* Median time from sign-in to acknowledging the first Alert (or confirming no Alerts need action) on a Doctor's Active Panel. Target: under 60 seconds. Validates FR-4 through FR-8.
- **SM-A2.** *Alert acknowledgment latency for between-session Alerts.* Median time from Alert creation to acknowledgment, measured only on Alerts created between Doctor Sessions — after the most recent Session ended (explicit sign-out or 8-hour inactivity timeout per §3.1) and before the next began. This isolates the metric to Alerts the portal helps surface (vs. Alerts the Doctor is already watching). Target: under 4 hours during clinic days. Validates FR-17, FR-19, FR-20.

**Secondary**

- **SM-A3.** *Supervisory access usage.* Share of sessions in which a Doctor reaches a Supervisory Patient via search. Target: 10–25% — high enough to prove the surface is used, low enough to confirm Active Panel remains primary. Validates FR-9 through FR-12.
- **SM-A4.** *Live Mode adoption.* Share of Detail View sessions that toggle Live Mode. Target: 5–15% — too high suggests the Active Panel isn't telling Doctors what they need from the snapshot. Validates FR-16.

**Counter-metrics (do not optimize)**

- **SM-AC1.** *Time-on-portal per Doctor per day.* Don't optimize upward. The portal earns its place by being fast; long sessions are a failure mode for periodic-review users. Counterbalances SM-A1.
- **SM-AC2.** *Alert volume.* Don't optimize upward via looser Thresholds. Alert fatigue is the failure mode that kills products like this. Counterbalances SM-A2.

## 8. Cross-Cutting NFRs

System-wide quality attributes the architecture and UX phases must hit. Per-feature NFRs (e.g., Live Mode multi-tab note in §4.4) sit with their features.

- **Performance — initial load.** Active Panel time-to-interactive under 1.5s on a typical clinic workstation (modern desktop browser, broadband), measured at Active Panel sizes up to the Active Panel Soft Cap (§3.1). Detail View open from Active Panel under 800ms perceived. The portal must feel faster than the EHR alongside it.
- **Performance — Live Mode.** Live Mode renders new Readings within the Live Mode Update Bound (§3.1) of their arrival at the back end, demonstrable by manual stopwatch on the fake API. No instrumented P95 target is encoded at PRD level; architecture may add one. Implementation mechanism (polling vs. server push) is left to architecture and not constrained by this NFR.
- **Security and server-authoritative authorization.** All Doctor Sessions over TLS. No Patient identifiers in URL query strings or logs. Session cookies HTTPOnly + SameSite=Lax minimum. Back-end authorization checks that any Patient a Doctor accesses is on either the Doctor's Active Panel or Supervisory Panel — never via client-side filtering only. Authorization is re-checked at render time and at acknowledgment-submission time; race conditions on membership changes are resolved server-side. The "new since last check" comparison is performed server-side using server timestamps; the client never participates in the comparison. On the fake API, this constraint is satisfied by ensuring the client never depends on client-only filtering for authorization decisions; the contract assumes the production back end enforces it. The architecture must make this dependency explicit.
- **Privacy.** No analytics calls carrying Patient identifiers (names, IDs, Readings). Error reports stripped of Patient context before transport. Even on fake data, the discipline is the discipline.
- **Accessibility.** WCAG 2.1 AA target for all interactive surfaces. Keyboard navigation across Active Panel, search, Detail View, and Acknowledge flows. Color is never the sole carrier of status (flagged/quiet/normal must use shape or text as well).
- **Reliability.** Active Panel and Detail View must degrade gracefully when the back end is partially available: a Patient with stale or missing data renders with an explicit "data unavailable" state, not a missing row or a crash.
- **Observability.** Front-end errors captured to a centralized log without Patient identifiers. Live Mode disconnect/reconnect events logged at session granularity. No PHI-equivalent fields in any log entry, even on fake data.

## 9. Aesthetic and Tone

### 9.1 Visual

- **Density done well.** The Active Panel must hold 30 rows comfortably without scrolling on a typical clinic monitor. Whitespace earns its place; nothing decorative.
- **Calm chromatic restraint.** Status is the only place color does work. *Flagged* uses a single high-attention hue (not red-by-default — explore amber/orange to avoid hospital-PA-system associations). *Quiet* uses a neutral muted tone. *Normal* uses none. No gradients, no glassmorphism, no decorative imagery.
- **Typographic clarity.** A grotesque sans for UI; a monospace or tabular-figures sans for Readings and timestamps so numbers align vertically. Numerals are first-class citizens.
- **Visual references.** Linear (density + restraint); Vercel dashboards at their cleanest; clinical software *aspirations* — what Epic and Cerner would look like if redesigned this decade.

### 9.2 Anti-references

- **Hospital software circa 2005.** Epic, Cerner, Meditech default UIs. Crowded, color-vomit, dropdown-driven, modal-heavy. The portal must not feel like that — even if its user is the same Doctor.
- **Consumer fitness aesthetics.** Apple Health gradients, Fitbit's celebratory tone, Whoop's neon. This is a clinical tool, not a wellness app.
- **Generic SaaS dashboard.** No "data viz playground" feel — no 12 chart types on screen, no every-color-of-the-rainbow legend.

### 9.3 Tone for system-generated text

- **Calm, factual, no exclamation.** Status copy is descriptive ("Monitor quiet for 4 hours"), not anthropomorphic ("This monitor needs your attention!").
- **No microcopy coddling.** No "Great work, doctor!", no thumbs-up confirmations, no celebratory toasts on acknowledging an Alert.
- **Error copy names what happened and what to do.** "Couldn't load Patient. Retry, or return to Active Panel." Not "Oops! Something went wrong."
- **Time is human-readable but precise.** "4 minutes ago" on the Active Panel; absolute timestamps in Alert history and acknowledgment metadata. Absolute timestamps render in the Doctor's browser timezone with an explicit TZ label; the back end stores UTC.

## 10. Platform

- **Desktop web, modern browsers.** Latest two stable versions of Chrome, Edge, Firefox, and Safari.
- **Desktop-first layout.** Designed for a clinic workstation; responsive enough to remain usable down to ~1280px but not optimized below.
- **No mobile or tablet targets in v1.** Doctors using a tablet or phone are explicitly not part of v1's user model.

## 11. Open Questions

These items are surfaced for the next phase (UX or architecture). None block downstream work.

1. **Fake API shape.** The PRD names the data the portal needs (Patients, Readings, Alerts, Monitor status, Active/Supervisory membership, plausible-bpm validation) but the fake API's exact contract is not specified here. To be designed in the architecture phase.
2. **Default Threshold values — cardiology sanity-check.** §3.1 lands 40 bpm lower / 120 bpm upper as v1 defaults so stories have real numbers. A sanity-check against cardiology references for outpatient telemetry should land before any clinical demo.
3. **Trend chart behavior with partial data and window-straddling Excursions.** §4.4 specifies the four regions and selectable windows, but the visual treatment of gaps (Monitor was Quiet within the window), of Excursions in progress at the window's left edge, and of DST transitions in time-axis rendering, is for UX to design.
4. **"Fresh open" definition for Trend window selection persistence.** FR-14 says window resets on a fresh Detail View open. Working definition: a navigation that creates a new history entry, or a page reload. Browser back to a prior Detail View entry restores its prior window selection. UX to confirm.
5. **Mid-session membership change behavior.** A Patient is removed from the Doctor's Active or Supervisory Panel between refreshes. The PRD specifies authorization is server-authoritative and re-checked, but the user-visible behavior on the Doctor's current surface (silently drop the row, show a "removed" state, etc.) is for UX.
6. **Empty-account global state.** A Doctor with zero Active and zero Supervisory Patients (brand-new account, or all assignments removed). UX to define the global empty state.
7. **Concurrent acknowledgment idempotency surfacing.** FR-19 specifies server idempotency on Alert ID and a discarded duplicate Note. UX to define how the second tab's "already acknowledged" state is presented.
8. **Threshold change during open Excursion.** v1 system-wide Thresholds may change between deploys. Working assumption: Thresholds are treated as immutable within a v1 deploy; if values change, all open Excursions are evaluated fresh against new bounds at the next Reading. Confirm with architecture.

## 12. Assumptions Index

No unresolved `[ASSUMPTION]` tags remain in this PRD. Outstanding inferences are tracked in §11 Open Questions.


