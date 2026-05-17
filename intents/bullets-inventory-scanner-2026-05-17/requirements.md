# Bookstore Inventory Scanner

Mobile shelf-level inventory tool for a small independent bookstore with ~8,000 SKUs.

- Scan an ISBN barcode using the iPhone back camera
- Show current stock level for the scanned title within 2 seconds of a successful scan
- Mark a title as on-shelf or missing with a single tap
- Allow an optional count adjustment with a required reason (damaged, miscounted, theft, other)
- Sync inventory changes to the existing Square inventory system
- Must keep working offline on the sales floor and reconcile when the phone is back on Wi-Fi
- Bilingual interface, English and Spanish, switchable per user without reinstalling
- Two part-time staff accounts plus the owner, no broader user management needed
- Total cost to the owner must stay under $200 across the first six months (app, hosting, any service fees combined)
- Not building a point-of-sale system, no customer checkout, no payment handling
- Not replacing the existing back-office laptop and USB scanner workflow, this runs alongside it
- No purchase-order or supplier-reordering features in scope
- If a clerk can scan 30 books in 10 minutes during a shelf check, it is working
- If the owner can trust the Monday morning sync without manually re-checking it, it is working
- What happens when two staff scan the same SKU offline and resolve to different counts before sync?
