# Spec Kernel: LedgerLite, a Bookkeeping Helper for Small Businesses

## Problem

Sole proprietors and very small business owners (1 to 5 employees) typically lack the time, training, and budget for full accounting software like QuickBooks or Xero. They end up tracking income and expenses in spreadsheets, shoeboxes of receipts, or not at all, which makes tax season painful and obscures the real financial health of the business. They need a lightweight tool that handles the basics without the learning curve or monthly cost of full accounting suites.

## Capabilities

- Delight users with a clean and modern bookkeeping experience
- Be intuitive for non-accountants to pick up and use
- Support productivity for busy small-business owners
- Feel modern and trustworthy, on par with best-in-class fintech apps
- Drive value by helping users stay on top of their finances
- Improve engagement so users return regularly to keep books current
- Be easy to use across desktop and mobile
- Provide peace of mind around tax readiness

## Constraints

- Single-user only in v1; no multi-seat or accountant collaboration
- Must run on macOS 13+ and modern Chromium-based browsers
- No direct bank integrations in v1 (CSV import only) to avoid Plaid licensing costs
- Local-first data storage; no cloud sync until v2
- Built by a solo developer with a 10-week budget before public beta

## Non-goals

- Not a replacement for a CPA or tax filing service
- No payroll, inventory, or invoicing in v1
- No support for multi-currency or international tax regimes
- No double-entry accounting; single-entry ledger only

## Success signal

Users love it and tell their friends, and the app feels like a natural part of their week.

---
Annotation: planted Spec Law violations — non-falsifiable capabilities.
