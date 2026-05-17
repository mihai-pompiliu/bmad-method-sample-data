# YearMark: Personalized Year-in-Review Book

**Status:** Draft v0.3
**Author:** Founder (product + ops)
**Date:** 2026-05-17
**Category:** Print-on-demand consumer product (physical artifact + custom web app)

## Problem

People generate enormous amounts of personal data every year (runs, songs, photos, places, friends tagged) and most of it dies in a Spotify Wrapped story view or a Strava annual recap that disappears in 72 hours.

There is no durable, object-shaped, shelf-worthy record of a year of a life. Generic photo books exist, but they require hours of layout work and they ignore the structured data (routes, tracks, song counts) that actually defines a modern year.

The result: a generation with the richest personal datasets in history and almost nothing on their bookshelf to show for it.

## Target customer

The "quantified-but-sentimental" user. Age 28 to 48. Runs, cycles, or trains seriously enough to have a Strava or Garmin history. Heavy Spotify or Apple Music listener. Takes 3000+ photos a year. Has disposable income for a $54 keepsake. Buys hardcover books, not just Kindle. The kind of person who already saves their bib numbers and finisher medals.

Secondary segment: gift-givers buying for that person, but explicitly out of scope for v1 (see Non-goals). Tertiary: parents documenting a child's year, also out of scope until we solve consent and data sourcing for minors.

## The physical product

A hardcover, library-stitched 60-page book.

- 7 inches by 10 inches (portrait orientation).
- 80lb acid-free interior stock with a matte finish so route lines and song-rank charts stay legible without glare.
- Cloth-bound spine in one of six colorways (forest, navy, oxblood, sand, slate, black).
- The customer's year and full name foil-stamped on both front cover and spine, in either gold or silver foil (matched to spine color via a sensible default, overridable).
- Optional soft-touch laminate dust jacket as a $9 add-on.
- Library stitching (Smyth-sewn) so the book lies flat when open, which matters because the monthly spreads run edge to edge.
- Endpapers in a complementary color, selected automatically from the cover palette.

What arrives in the box: the book itself, wrapped in unbleached tissue paper, inside a recyclable kraft mailer. A single printed card with a short note from the founder, a QR code linking to the customer's online flipbook version, and a one-line reorder hint. No plastic, no foam, no filler.

## The digital surface

Three phases plus a post-purchase layer: connect, design, order, then ownership.

**Connect.** The customer signs in (email + magic link, no password). They authorize data sources via OAuth where supported: Strava, Garmin Connect, Google Photos. Spotify is partial (the Wrapped API is limited and historical data older than the current year is gated), so we fall back to a CSV export flow for Spotify and for any source the user prefers to provide manually. We import, dedupe, and structure the data into a normalized internal schema (activities, tracks, photos, places, social tags).

**Design.** A browser-based design tool generates a default 60-page layout in under 90 seconds. The customer can: drag-to-reorder spreads, swap between three layout density presets (sparse, balanced, dense), pick from four typeface pairings, pick the cover color from the six options, and toggle individual spreads on or off. Every spread is a monthly canonical template: top three routes rendered as inline maps, top five tracks with play counts, the month's most-photographed location with one hero photo, the month's first run and last run side by side, friends tagged most often that month. A final-preview flipbook renders in-browser plus an optional PDF download for the customer's records.

**Order.** Stripe Checkout. Stripe Tax handles US sales tax and international VAT. Shipping calculated at checkout. Order confirmation is immediate, with manufacturing status updates by email at three milestones (queued, on press, shipped with tracking).

**Post-purchase.** The customer keeps lifetime access to their flipbook version at a private URL. They can reorder a prior year's exact book at any time (the design is versioned and frozen at order time). New-year notifications go out each January once their previous year's data is queryable.

## Manufacturing partners and fulfillment

Primary print partner: **Ridgeline Bindery** (US-based, Minnesota), a small-batch hardcover specialist that handles foil stamping and Smyth sewing in-house. They run a Heidelberg sheet-fed offset press for runs above 100 units and digital HP Indigo for one-offs, which matters for our small-batch launch phase.

Backup partner under evaluation for surge capacity and West Coast shipping cost savings. Onboarding plan is parallel proofing across both partners on a sample dataset before any traffic is routed.

Lead time of 8 to 14 calendar days from order placed to label printed. Domestic shipping via UPS Ground (2 to 5 days). International via DHL Express with DDP terms so the customer pays no surprise duties at the door.

Returns are limited: custom-printed product is not generally returnable, but damaged-on-arrival cases trigger a free replacement within a 30-day window from delivery. Lost-in-transit cases follow the carrier's claim timeline and we replace on confirmation. We absorb the cost of replacement and book it as a quality-control line item, not a refund.

## Functional requirements

1. OAuth integration with Strava, Garmin Connect, Google Photos. Spotify via partial API plus CSV.
2. CSV upload fallback for any supported data type.
3. Normalize all imported data into an internal schema for layout.
4. Generate a default 60-page layout in under 90 seconds for the 95th-percentile dataset.
5. In-browser design tool: reorder spreads, swap fonts, swap density, swap cover color, toggle spreads.
6. In-browser flipbook preview at final-quality resolution.
7. PDF download of the final book for the customer.
8. Stripe Checkout with Stripe Tax (US sales tax, international VAT, DDP-ready).
9. Order tracking page with three-state manufacturing status plus carrier tracking.
10. Reorder flow for a prior year's frozen design.
11. Magic-link auth, no password.
12. Email notifications at the three manufacturing milestones.
13. Print-ready PDF handoff to the print partner via their existing intake API or SFTP drop.

## Constraints

**Physical.**
- Cover foil stamping has a tolerance of roughly 0.5mm. Names longer than 22 characters require font auto-shrink; longer than 28 require a layout break (first name on one line, last on another).
- 60 pages is fixed for v1. Variable page counts complicate spine width and cover die-cuts.
- Cloth color batches vary slightly between print runs. We accept this as character, not defect.

**Digital.**
- PDF generation pipeline must complete under 90 seconds for the most complex configuration (heavy photo set, dense layout, full map rendering).
- Google Photos API rate limits cap us at roughly 10,000 requests per user per day. We must page intelligently and cache thumbnails.
- We do not store full-resolution Google Photos copies beyond the rendering window (data minimization). Thumbnails for the flipbook preview are kept; full-res is discarded after the print PDF is locked.
- CMYK conversion is the hardest fidelity problem. In-browser preview is sRGB. We apply a calibrated CMYK profile matched to Ridgeline's press. Tolerance is "reasonable" but cannot be pixel-perfect.

**Commerce.**
- COGS target: under $22 at small-batch volumes (sub-500 books per month). Under $16 at 5000+ books per year. Foil stamping and cloth are the swing costs.
- Retail $54 at launch. Price drops follow volume tier achievements, not calendar.
- Stripe Tax must be configured for every state and every supported international destination before launch.
- DHL DDP shipping requires HS codes and accurate item value declared per book.

## Non-goals

- Not a generic photo book product. The templating is opinionated and year-in-review-shaped. If a customer wants a wedding album, they should buy a wedding album elsewhere.
- Not mobile-first design. Responsive web is sufficient. The design tool is best on a tablet or laptop, and we will say so.
- Not a gift-buying flow in v1. The data-owner makes the book for themselves. Gifting requires data-sharing patterns we are not solving yet.
- Not building our own print facility. POD partner model only. We may revisit at 50,000+ books per year.
- Not a Spotify Wrapped competitor. We are downstream of these data sources. If Spotify, Strava, or Garmin shuts off their APIs, we degrade to CSV upload, but we do not generate the underlying datasets ourselves.
- Not editorial or human-curated yearly summaries. Algorithmic plus user-edited only. No staff writer touches a customer's book.

## Success signal

Within 12 months of launch: 5000 books shipped, NPS above 60, reorder rate (year N+1) above 35 percent of year N buyers, damaged-on-arrival rate below 1.5 percent, and at least one organic moment where a customer posts the unboxing to social and it travels.

## Open questions

1. Do we offer a leather-spine upgrade for v1, or hold for v2? Cost adds roughly $9 COGS.
2. Is Spotify's partial API stable enough to ship without the CSV fallback being the primary path? Likely no, but worth one more conversation with their developer relations team.
3. How do we handle a customer who connects mid-year (June 2026) and wants the book covering June 2025 to June 2026 rather than the calendar year? Defer to v1.1 or solve at launch?
4. Friends tagged appears in monthly spreads. Privacy implications if a tagged friend has not consented to appearing in someone else's printed book. Likely fine (it is a personal artifact, not published), but worth a quick legal review.
5. Backup print partner: do we onboard before launch (insurance) or after first surge (capital efficient)?
6. Cover color preview fidelity in-browser is approximate. Do we ship physical swatch cards as a $4 pre-order, or accept the variance?
