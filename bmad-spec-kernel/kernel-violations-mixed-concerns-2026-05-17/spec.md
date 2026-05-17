# Spec Kernel: SMB Customer Feedback Collection Tool

## Problem

Small and mid-sized businesses struggle to collect, organize, and act on customer feedback across email, in-app prompts, and post-purchase surveys. Our company strategy calls for owning the "voice of customer" layer for the SMB segment by 2027, which is why this product is a Q3 priority. To solve this, we will build a multi-channel feedback widget, an inbox-style review surface for owners, a sentiment-tagging engine, and an integrations layer that syncs to HubSpot and Slack. The widget should load in under 200ms and feel native on mobile.

## Capabilities

- Embeddable JavaScript widget that supports NPS, CSAT, and open-text prompts.
- Owner inbox that consolidates feedback from email, widget, and SMS into a single feed.
- Automatic sentiment tagging (positive, neutral, negative) on every entry.
- Must use Postgres as the primary datastore and Redis for queueing.
- Must achieve a 30% response rate on post-purchase survey prompts within the first 60 days of launch.
- Weekly digest email summarizing top themes per business.

## Constraints

- Initial release targets US-based SMBs only; we will not localize for EU or APAC in v1.
- No native mobile app; web-only experience.
- We are not building a full helpdesk or ticketing system, and we will not replace Zendesk for any customer.
- Stripe is the only supported billing provider for the first release.
- The team is three engineers and one designer through end of Q3.

## Non-goals

- We do not want to build a CRM, but the inbox should sync with HubSpot contacts and surface deal stages next to each feedback entry.
- We are not building outbound marketing email, though we should support trigger-based follow-up emails when sentiment is negative.
- No AI-generated reply drafts in v1, but the system should auto-suggest reply templates ranked by past acceptance rate.
- No public review site, although verified positive reviews should be exportable as embeddable testimonial widgets for the customer's own marketing site.

## Success signal

- Customers love it.
- Lots of feedback gets collected across channels.
- Owners check the inbox regularly and feel in control of their reputation.
- HubSpot sync "just works" and support tickets about it stay low.
- The team feels proud of the launch.
- Investors see clear traction in the SMB segment.
- Churn goes down compared to businesses that don't use the tool.

---
Annotation: planted Spec Law violations — concerns mixed across fields.
