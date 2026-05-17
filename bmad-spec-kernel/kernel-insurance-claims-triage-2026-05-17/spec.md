## Problem

A mid-sized regulated property insurance carrier receives first-notice-of-loss (FNOL) claims faster than adjusters can route them. Today, every new claim sits in a shared queue until a human adjuster opens it, reads the narrative, and decides whether it is a low-complexity express claim, a standard claim, or a high-severity claim needing a senior adjuster or special investigations unit (SIU). Sorting delay extends cycle time, frustrates policyholders, and causes high-severity losses to be detected late, increasing leakage and regulatory exposure.

## Capabilities

- Ingest a structured FNOL record (policy ID, loss type, loss date, reported amount, narrative text, prior-claims flag) from the claims system of record.
- Classify each FNOL into one of three severity tiers (express, standard, high-severity) within 60 seconds of receipt.
- Emit a tier decision plus a ranked list of the top three features that drove the classification, stored alongside the claim record.
- Flag any claim whose narrative or metadata matches configured SIU referral rules and route it to the SIU queue regardless of tier.
- Allow a licensed adjuster to override the tier, capture the override reason, and persist both the original and overridden tier.
- Expose a daily report of volume, tier mix, override rate, and average time-to-tier per loss type.

## Constraints

- All PII and claim narratives must be processed and stored in-region; no offshore processing or storage of policyholder data.
- Every tier decision must carry a human-readable explanation retained for the statutory claim retention window (minimum 7 years).
- The model and its training data must be version-pinned and auditable on request by the state insurance regulator.
- The system must defer to the human adjuster on any claim where model confidence falls below a configured threshold.
- No customer-facing communication may be generated or sent by the system.

## Non-goals

- Does not automate payout, reserve setting, or coverage determination.
- Does not replace the SIU investigator or the licensed adjuster of record.
- Does not ingest unstructured attachments (photos, PDFs, audio) in this iteration.
- Does not handle commercial lines; personal property lines only.

## Success signal

Median time from FNOL receipt to assigned severity tier drops from the current 6 hours to under 5 minutes across a 30-day production window.
