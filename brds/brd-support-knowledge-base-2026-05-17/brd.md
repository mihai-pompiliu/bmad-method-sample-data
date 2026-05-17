# Business Requirements Document: Support Knowledge Base

Document ID: BRD-SKB-2026-05-17
Date: 2026-05-17
Owner: Director of Customer Support
Status: Draft for IT and Engineering review

## Executive Summary

Our support team is drowning. Ticket volume is up roughly 40% year over year and we have not added headcount to match. The CEO has asked us to "go build a real KB" so customers can self-serve and our agents can stop answering the same questions over and over. This document captures the business need at a high level. Product and technical details are deferred to IT and Engineering, who will own the build.

The goal is straightforward: stand up a knowledge base that customers and internal agents can search, that contains articles on our most common issues, and that measurably reduces ticket volume.

## Business Objectives

1. Reduce inbound support ticket volume by 25% within 6 months of launch.
2. Achieve a ticket deflection rate of 30% (measured as KB sessions that do not result in a ticket within 24 hours).
3. Cut average handle time by 15% by giving agents faster access to canned answers.
4. Improve CSAT by at least 5 points (assumed downstream effect of faster answers).

## Stakeholders

| Role | Name | Responsibility |
|------|------|----------------|
| Executive Sponsor | CEO | Funding, strategic alignment |
| Business Owner | Director of Customer Support | Requirements, sign-off |
| Technical Owner | VP Engineering | Build and delivery |
| IT Lead | Head of IT | Platform selection, integration |
| Product Input | Product Manager (TBD) | Feature prioritization |
| End Users | Support Agents, Customers | Daily use |

## Scope

### In Scope
- Public-facing knowledge base accessible from our marketing site and in-app help.
- Internal knowledge base for support agents (may be the same system with permissions).
- Article authoring, categorization, and search.
- Basic analytics on article views and search terms.

### Out of Scope
- Community forums.
- Live chat or chatbot deployment (separate initiative).
- Translation and localization (phase 2).
- Migration of legacy wiki content (will be evaluated separately).

## Functional Requirements

- FR-001: The system shall allow authorized users to create, read, update, and delete knowledge base articles.
- FR-002: The system shall support categorization of articles into a hierarchy of categories and subcategories.
- FR-003: The system shall provide full-text search across all published articles.
- FR-004: The system shall support both public (customer-facing) and private (internal-only) article visibility.
- FR-005: The system shall be intelligent.
- FR-006: The system shall allow users to vote articles as helpful or not helpful and leave optional feedback.
- FR-007: The system shall maintain version history for each article and allow rollback to a prior version.
- FR-008: The system shall support rich text formatting, images, and embedded video in articles.
- FR-009: The system shall use AI to suggest relevant articles to customers and agents.
- FR-010: The system shall provide a tagging mechanism for articles.
- FR-011: The system shall integrate with our existing ticketing platform so agents can attach articles to tickets.
- FR-012: The system shall be easy to use.

## Non-Functional Requirements

- NFR-001: The KB shall be available 99.5% of the time.
- NFR-002: Search results shall return within 2 seconds for typical queries.
- NFR-003: The system shall support at least 10,000 concurrent users.
- NFR-004: The system shall be mobile-responsive.
- NFR-005: The system shall comply with our existing SSO and access control policies.

## Assumptions

- IT and Engineering will evaluate build versus buy and recommend a platform.
- Existing support content (scattered across Google Docs, Notion, and old tickets) is sufficient seed material.
- Support agents will contribute articles as part of their normal workflow.
- Customers will find and use the KB if it exists and search works.

## Success Criteria

- Deflection rate of 30% measured 6 months post-launch.
- 25% reduction in inbound ticket volume on topics covered by the KB.
- At least 100 published articles within 60 days of launch.
- KB search used in at least 50% of support sessions.

## Sign-off

| Name | Role | Signature | Date |
|------|------|-----------|------|
| | CEO | | |
| | Director of Customer Support | | |
| | VP Engineering | | |
| | Head of IT | | |
