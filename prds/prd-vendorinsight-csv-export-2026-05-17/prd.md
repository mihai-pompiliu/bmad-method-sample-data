# VendorInsight CSV Export PRD (Phase 1.5)

**Status:** Draft
**Owner:** Procurement Product Team
**Last updated:** 2026-05-17

## Context

VendorInsight shipped with PDF export only (see FR-10 in the base PRD). Since launch, procurement managers and the annual auditor have asked for CSV so they can pull comparison and decision data into their own spreadsheets and audit workpapers. The original ask was "exports" in general; we negotiated this down to two surfaces and one format to keep the change small.

## The Ask

Add CSV export to two existing surfaces in VendorInsight: the vendor comparison view and the decision record. Procurement managers want to drop active comparisons into internal reports without retyping. The annual auditor wants to pull a fiscal-year set of decision records into their workpaper template. This is a narrow, user-initiated export from screens that already exist. No new surfaces, no new data, no new scheduling.

## Functional Requirements

**FR-1: Export Comparison to CSV.** From the vendor comparison view, a user shall be able to export the currently active comparison (selected vendors, all four dimensions, sub-criteria, weighted total) to a single CSV file.

**FR-2: Export Decision Record to CSV.** From a decision record, a user shall be able to export the structured decision data (shortlisted vendors, final scores per dimension, weighted total, chosen vendor, written rationale, decision date, decision owner) to a single CSV file.

**FR-3: CSV Format.** Exported files shall be UTF-8 encoded and conform to RFC 4180 (quoted fields, CRLF line endings, escaped quotes). Filename shall include surface, identifier, and ISO date (for example, `comparison-CAT042-2026-05-17.csv`).

**FR-4: Permission-Scoped Content.** Export shall include only rows the requesting user is authorized to see under existing VendorInsight role rules. A buyer's export reflects their visible categories. An auditor's export reflects their configured audit window.

**FR-5: Audit Log Entry.** Every export shall write an entry to the existing audit log capturing exporter identity, surface type, target identifier, row count, filename, and timestamp.

## Constraints

- Permissions and auditor window scoping are enforced server-side. The client cannot widen the result set.
- Every export produces exactly one audit log row, written before the file is returned to the user.
- A 5,000-row export must complete in under 8 seconds at the 95th percentile on production hardware.

## Non-Goals

- No Excel (.xlsx) export. CSV only. If users want Excel, they can open the CSV in Excel.
- No scheduled, recurring, or saved-configuration exports. Every export is manual and user-initiated.
- No PDF or other format changes. PDF already exists per the base PRD and is unchanged here.
- No export of evidence attachments. Structured score and decision data only. Attachments stay in-app.
- No bulk-export API, webhook, or programmatic endpoint. UI-triggered export only.

## Success Signal

Within one quarter of launch, the annual auditor and at least half of active procurement managers use CSV export at least once per month, and no audit-log gaps or permission-leak findings are reported.

## Open Questions

1. Do we need a localized number format (decimal comma) for any regional users, or is en-US sufficient for v1?
2. Should the decision-record CSV flatten multi-vendor shortlists into one row per vendor, or one row per decision with vendor columns? (Leaning one row per vendor.)
3. Is there a max row cap we want to enforce on auditor exports, or do we rely on the 8-second performance budget as the natural ceiling?
