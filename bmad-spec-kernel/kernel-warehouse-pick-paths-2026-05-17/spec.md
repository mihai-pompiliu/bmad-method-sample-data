## Problem

Pickers at a 50-person fulfillment center walk redundant loops because the daily pick list is printed in order-entry sequence, not bin-location order. Walking accounts for a large share of pick time, and naive shortest-path reordering causes damage when heavy items are stacked on fragile ones or when fragile items are carried for long stretches. Supervisors need a tool that re-sequences each picker's list to shorten total walking distance while respecting weight-on-top and fragility rules, and that runs on the handheld scanners pickers already carry.

## Capabilities

- Ingest the day's pick list (order id, SKU, quantity, bin coordinates, item weight, fragility flag) from a CSV or REST endpoint.
- Produce a re-sequenced pick path per picker that minimizes estimated walking distance using bin aisle and shelf coordinates.
- Enforce stacking rules: heavier items picked before lighter ones in the same tote, and fragile items routed so they are not crushed or carried across more than a configurable distance.
- Render the re-sequenced list on a 480p handheld touchscreen with large tap targets, current bin highlighted, and a one-tap "skip and return" for blocked bins.
- Cache the active route on the device so a picker can complete their list during a wifi outage and sync completion events when the connection returns.
- Log per-pick timing and route deviations so supervisors can compare actual versus estimated walk time per shift.

## Constraints

- Must run on existing Zebra-class handheld scanners with 480p touchscreens, single-hand operation, and physical scan trigger.
- Operates over warehouse wifi that drops for up to several minutes at a time; no operation may require a live connection once the shift's list is loaded.
- Initial release covers one warehouse floor plan; aisle and bin coordinates are supplied as a static map file.
- Re-sequencing for a 200-line pick list must complete in under 3 seconds on the handheld.
- No PII beyond picker employee id; route logs retained 30 days.

## Non-goals

- Not a warehouse management system replacement; does not own orders, shipments, or returns.
- Does not track inventory levels, replenishment, or cycle counts.
- Does not assign orders to pickers or balance workload across the team.
- Does not optimize across multiple warehouses or dynamic slotting changes mid-shift.

## Success signal

Median walking time per order drops at least 18 percent versus the prior 30-day baseline within the first full week of rollout, with no increase in damaged-item reports.
