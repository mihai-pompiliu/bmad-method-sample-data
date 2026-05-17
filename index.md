---
title: BMad Method Sample Data
purpose: Curated, hand-shaped sample inputs and artifacts for testing and demonstrating BMad Method skills.
total_entries: 102
last_updated: 2026-05-17
---

# BMad Method Sample Data

A deliberate cross-section of real-shaped inputs and artifacts spanning the full BMad funnel. Each entry is hand-shaped to test something specific. The repo exists to:

1. **Test BMad skills against realistic inputs** at every density and shape (one-liner intents through 900-line approval-ceremony PRDs).
2. **Train downstream skills** (architecture, UX, ticketing) on the range of upstream artifacts they will actually receive.
3. **Validate the spec-kernel thesis** — does a five-field kernel carry enough signal for downstream, or is the full PRD wrapper irreducible?
4. **Demonstrate the BMad Method** publicly via case studies built end-to-end from selected entries.

## How the repo is organized

Folders are ordered roughly upstream to downstream along the BMad funnel.

| Folder | What lives here | Count |
|---|---|---|
| [`intents/`](intents/) | Minimum-density inputs: one-liner intents, tweets, SMS-style threads, bullet-list-only requirements, README stubs, pre-written spec kernels | 7 |
| [`braindumps/`](braindumps/) | Founder-energy raw inputs: rants, urgency memos, stealth blog posts, shower-thought streams, multi-day chat dumps, customer-coffee post-mortems | 9 |
| [`transcripts/`](transcripts/) | Verbal-source captures: voice memos, Loom-style narrations, scribed meeting notes, podcast monologues, Zoom interviews, walking-meeting two-voice transcripts | 6 |
| [`threads/`](threads/) | Multi-voice conversation captures: Slack threads, email threads, X/Twitter threads, GitHub issue discussions | 6 |
| [`research/`](research/) | Research outputs feeding the PRD altitude: customer-interview synthesis, market analysis | 1 |
| [`bmad-spec-kernel/`](bmad-spec-kernel/) | Pre-written five-field Spec kernels — both clean exemplars and deliberately-violating studies | 8 |
| [`brds/`](brds/) | Business Requirements Documents — corporate-style "the system shall" docs, light on product framing | 6 |
| [`briefs/`](briefs/) | Product briefs (one altitude above PRDs); standalone briefs and brief→PRD pairs | 6 |
| [`prds/`](prds/) | Formal product specifications at the PRD altitude: PRDs, PRFAQs, OSS RFCs, JTBD job-stories specs | 53 |
| [`architectures-solution-designs/`](architectures-solution-designs/) | Downstream architecture and solution-design artifacts | (placeholder) |
| [`projects/`](projects/) | End-to-end project examples (single intent walked through the full BMad chain) | (placeholder) |
| [`other/`](other/) | Catch-all | (placeholder) |

Density spectrum across the corpus: **3 lines** (one-liner intent, three-line pitch) to **890 lines** (HiveLink maximalist PRD). Median ~130 lines.

## Quick stats

| Metric | Value |
|---|---|
| Total entries (active) | 102 |
| Languages represented | English (primary), Spanish (1 PRD) |
| Entries with planted Spec Law violations, contradictions, or hallucinations | 10 |
| Brief→PRD altitude-fidelity pairs | 2 (PomodoroLite, HiveLink) |
| Demo-worthy build-and-publish candidates | 12 (flagged in catalog) |
| AI-native products | 6 |
| AI as primary feature | 7 |

---

## How to use this corpus for skill testing

| Skill | Recommended inputs |
|---|---|
| **bmad-spec** | All of `intents/`, `braindumps/`, `transcripts/`, `threads/`, `research/`, `briefs/`, `brds/`, `prds/`. The universal-distiller thesis depends on extraction working across all of these. |
| **bmad-product-brief** | `briefs/` for shape reference; `braindumps/` + `intents/` + `brds/` as inputs to test brief generation |
| **bmad-create-prd** | `briefs/` and `brds/` as input; `prds/` as output reference shapes |
| **bmad-validate-prd** | `prds/prd-junior-pm-bad-*` (10 planted anti-patterns), `intents/kernel-rss-revival-*` (planted Spec Law violations), `briefs/brief-quiet-social-*` (6 contradictions), `briefs/brief-llm-fitness-platform-*` (planted hallucinations), `bmad-spec-kernel/kernel-violations-*` (4 distinct Spec Law violation studies), `brds/brd-hr-onboarding-revamp-*` (internal contradiction), `brds/brd-compliance-audit-trail-*` (impossible requirement smuggled mid-list) |
| **bmad-prfaq** | `prds/prfaq-snapboard-*` (consumer), `prds/prfaq-internal-runbook-builder-*` (internal), `prds/prfaq-meetingdoctor-*` (internal dogfood), `prds/prfaq-rendezvous-*` (consumer mobile), `prds/prfaq-shipnow-launchpad-*` (developer platform) for shape reference |
| **bmad-create-ux** | `prds/*` — measure whether UX output degrades when fed only the bmad-spec kernel vs. the full PRD |
| **bmad-create-architecture** | Same falsification experiment as above on architecture output |
| **bmad-create-epics-and-stories** | Same falsification experiment for ticketing |
| **bmad-quick-dev** | Any of `intents/`, `braindumps/`, `briefs/` — what does Quick Dev do with thin vs. thick inputs? |
| **bmad-investigate** | `prds/prd-aurora-migration-*`, `prds/prd-engineering-heavy-cache-*` — brownfield system investigation rehearsal |
| **bmad-distillator** | Any maximalist entry (`prds/prd-hivelink-*`, `prds/prd-medivault-*`), or any BRD — lossless compression test |
| **bmad-edit-prd** | `prds/prd-pomodorolite-*` (lean, simple edits); `prds/prd-junior-pm-bad-*` (substantive cleanup target) |
| **bmad-shard-doc** | `prds/prd-hivelink-*`, `prds/prd-medivault-*` — maximalist shard targets |
| **bmad-document-project** | `prds/prd-aurora-migration-*` style brownfield documentation rehearsal |
| **bmad-kernel-validate** (if/when it exists) | All of `bmad-spec-kernel/` — 4 clean exemplars to verify pass-through, 4 deliberate-violation studies to verify detection |
| **bmad-bmb (BMad Builder) router** | See the "BMad-bmb reroute candidates" section below — entries whose described product is actually a BMad agent, skill, or workflow in disguise. Tests whether a router skill correctly identifies "this should be built inside BMad, not as a SaaS." |

---

## Catalog — full corpus

### `intents/` — minimum-density inputs (7)

| Entry | Lines | Notes |
|---|---|---|
| [intent-podcast-sync](intents/intent-podcast-sync-2026-05-17/intent.md) | 3 | Single-sentence intent. Floor case — should bmad-spec block or distill? |
| [intent-tweet-i-want-to-build](intents/intent-tweet-i-want-to-build-2026-05-17/intent.md) | 3 | Single tweet under 280 chars; "anyone want to build this with me" energy |
| [intent-three-line-pitch](intents/intent-three-line-pitch-2026-05-17/intent.md) | 3 | Three-line elevator pitch (who/give/so-they-can) |
| [intent-text-to-cofounder](intents/intent-text-to-cofounder-2026-05-17/intent.md) | 13 | SMS-style late-night text thread; product seed emerges via abbreviated dialogue |
| [bullets-inventory-scanner](intents/bullets-inventory-scanner-2026-05-17/requirements.md) | 19 | 15 bullets, no prose. Tests "is structure alone enough?" |
| [kernel-rss-revival](intents/kernel-rss-revival-2026-05-17/spec.md) | 58 | Pre-written five-field kernel with planted Spec Law violations (CAP-3 missing success, CAP-4 prescribes HOW, decorative constraint, partial Rule 5 violation) |
| [intent-readme-stub](intents/intent-readme-stub-2026-05-17/README.md) | 68 | GitHub-README skeleton written BEFORE any code; pre-alpha self-test |

### `braindumps/` — founder-energy raw inputs (9)

| Entry | Lines | Notes |
|---|---|---|
| [braindump-shower-thought-monetization](braindumps/braindump-shower-thought-monetization-2026-05-17/braindump.md) | 29 | One-thought-explodes shape; indie maker monetizing a free public API |
| [braindump-conference-hallway-idea](braindumps/braindump-conference-hallway-idea-2026-05-17/braindump.md) | 74 | Excited, scattered phone notes after a SaaStr onboarding talk |
| [braindump-post-call-memo](braindumps/braindump-post-call-memo-2026-05-17/memo.md) | 83 | Hot-take urgency memo after a customer call (renewal at stake) |
| [braindump-shelf-scanner](braindumps/braindump-shelf-scanner-2026-05-17/braindump.md) | 97 | Shower-thought stream proposing a phone-camera bookshelf inventory |
| [braindump-after-customer-coffee](braindumps/braindump-after-customer-coffee-2026-05-17/braindump.md) | 99 | Post-coffee braindump; load-bearing customer quotes + counter-hypothesis |
| [braindump-stealth-blog](braindumps/braindump-stealth-blog-2026-05-17/blogpost.md) | 111 | Stealth blog post (essay form) proposing a forward-looking finance product |
| [braindump-standup-killer](braindumps/braindump-standup-killer-2026-05-17/braindump.md) | 125 | Founder rant proposing an AI-async-standup tool. *Demo candidate.* |
| [braindump-2am-founder-rant](braindumps/braindump-2am-founder-rant-2026-05-17/braindump.md) | 192 | Solo founder, 2am, after losing a customer; ALL CAPS bursts, contradictions, no clear ask |
| [braindump-discord-paste](braindumps/braindump-discord-paste-2026-05-17/paste.md) | 257 | 5-day Discord thread converging on a snapshot/rollback feature |

### `transcripts/` — verbal-source captures (6)

| Entry | Lines | Notes |
|---|---|---|
| [transcript-podcast-monologue](transcripts/transcript-podcast-monologue-2026-05-17/transcript.md) | 39 | 12-min solo-podcast monologue on therapist billing pain; filler words, stutters, timestamps |
| [notes-meeting-mobile](transcripts/notes-meeting-mobile-2026-05-17/notes.md) | 117 | Scribed meeting notes from a mobile app v2 kickoff |
| [voicememo-dog-walk-route](transcripts/voicememo-dog-walk-route-2026-05-17/voicememo.md) | 131 | Rambling voice memo with backtracks — dog-walk-route app idea |
| [braindump-loom-transcript](transcripts/braindump-loom-transcript-2026-05-17/transcript.md) | 137 | Loom-style narration walking through a 1:1 manager tool sketch |
| [transcript-zoom-customer-interview](transcripts/transcript-zoom-customer-interview-2026-05-17/transcript.md) | 252 | Otter-style Zoom interview with creative-agency Ops Lead on project handoffs; load-bearing quote + magic-wand reveal |
| [transcript-walking-meeting](transcripts/transcript-walking-meeting-2026-05-17/transcript.md) | 297 | Two-voice walking transcript with `[wind]` / `[inaudible]` artifacts; Product vs Eng arguing whether to kill, sunset, or invest in Pulse Reports |

### `threads/` — multi-voice conversation captures (6)

| Entry | Lines | Notes |
|---|---|---|
| [thread-x-launchpad](threads/thread-x-launchpad-2026-05-17/thread.md) | 158 | 30-tweet X/Twitter thread proposing an indie-hacker launch tool |
| [thread-pricing-page-rebuild](threads/thread-pricing-page-rebuild-2026-05-17/thread.md) | 190 | Multi-voice Slack thread, decisions implicit, contradictions present |
| [thread-email-cancellation-feature](threads/thread-email-cancellation-feature-2026-05-17/thread.md) | 195 | 7-email reply chain with accumulating `>` quotes; analytics platform losing a $96k account |
| [thread-github-issue-discussion](threads/thread-github-issue-discussion-2026-05-17/thread.md) | 231 | 19-comment OSS issue thread on a data-validation library union-type ask; ends `needs-design` |
| [thread-email-roi-tool](threads/thread-email-roi-tool-2026-05-17/thread.md) | 258 | 3-week email thread, 5 execs, ROI-calculator decision arc |
| [thread-slack-pricing-debate](threads/thread-slack-pricing-debate-2026-05-17/thread.md) | 262 | 5-speaker Slack debate: seat-based vs consumption pricing for B2B observability; unresolved ending |

### `research/` — research outputs (1)

| Entry | Lines | Notes |
|---|---|---|
| [synthesis-customer-interviews](research/synthesis-customer-interviews-2026-05-17/synthesis.md) | 212 | UX research synthesis from 14 small-business onboarding interviews |

### `bmad-spec-kernel/` — pre-written five-field kernels (8)

| Entry | Lines | Notes |
|---|---|---|
| [kernel-warehouse-pick-paths](bmad-spec-kernel/kernel-warehouse-pick-paths-2026-05-17/spec.md) | 31 | Clean exemplar — pick-list re-ordering for a fulfillment-center operator |
| [kernel-insurance-claims-triage](bmad-spec-kernel/kernel-insurance-claims-triage-2026-05-17/spec.md) | 31 | Clean exemplar — regulated FNOL triage for a property carrier |
| [kernel-podcast-clip-extractor](bmad-spec-kernel/kernel-podcast-clip-extractor-2026-05-17/spec.md) | 32 | Clean exemplar — indie-podcaster shareable-clip surfacer |
| [kernel-edu-quiz-builder](bmad-spec-kernel/kernel-edu-quiz-builder-2026-05-17/spec.md) | 33 | Clean exemplar — teacher-built formative quizzes from a lesson plan |
| [kernel-violations-non-falsifiable](bmad-spec-kernel/kernel-violations-non-falsifiable-2026-05-17/spec.md) | 38 | **Planted violation:** capabilities are non-observable ("delight", "intuitive", "best-in-class") |
| [kernel-violations-solution-disguised](bmad-spec-kernel/kernel-violations-solution-disguised-2026-05-17/spec.md) | 38 | **Planted violation:** capabilities pre-bake UI choices (dashboard, sidebar, wizard, kanban) |
| [kernel-violations-mixed-concerns](bmad-spec-kernel/kernel-violations-mixed-concerns-2026-05-17/spec.md) | 42 | **Planted violation:** problem leaks capabilities; constraints contain non-goals; non-goals smuggle aspirational capabilities |
| [kernel-violations-vendor-soup](bmad-spec-kernel/kernel-violations-vendor-soup-2026-05-17/spec.md) | 47 | **Planted violation:** capabilities name vendors/frameworks (Stripe, AWS Lambda, Pinecone, Next.js, etc.) |

### `brds/` — Business Requirements Documents (6)

| Entry | Lines | Notes |
|---|---|---|
| [brd-support-knowledge-base](brds/brd-support-knowledge-base-2026-05-17/brd.md) | 90 | Thin/lazy KB BRD; "FR-005 the system shall be intelligent" style; zero authoring/maintenance plan |
| [brd-sales-quote-generator](brds/brd-sales-quote-generator-2026-05-17/brd.md) | 113 | RevOps quote-gen; smuggled vendor preference (FR-011 names DocuSign) |
| [brd-hr-onboarding-revamp](brds/brd-hr-onboarding-revamp-2026-05-17/brd.md) | 119 | VP of People onboarding BRD; **internal contradiction** between FR-005 and FR-009 (equipment self-service) |
| [brd-finance-month-end-close](brds/brd-finance-month-end-close-2026-05-17/brd.md) | 128 | Controller close BRD; FR-013 conflates Blackline tool with need; FR-018 is a wish ("continuous close") |
| [brd-compliance-audit-trail](brds/brd-compliance-audit-trail-2026-05-17/brd.md) | 138 | CCO regional-bank audit-trail BRD; **planted near-impossible requirement** (FR-014 retroactive backfill of historical audit events) |
| [brd-procurement-vendor-portal](brds/brd-procurement-vendor-portal-2026-05-17/brd.md) | 164 | Procurement portal BRD; 20 FRs, throughput-oriented success criteria, "user-friendly" smuggled in |

### `briefs/` — product briefs (6)

| Entry | Lines | Pair? | Notes |
|---|---|---|---|
| [brief-coffeebean-tracker](briefs/brief-coffeebean-tracker-2026-05-17/brief.md) | 31 | standalone | Thin one-pager brief (personal coffee log) |
| [brief-pomodorolite](briefs/brief-pomodorolite-2026-05-17/brief.md) | 61 | →pomodorolite | **Brief→PRD pair.** Lean hobby altitude. |
| [brief-llm-fitness-platform](briefs/brief-llm-fitness-platform-2026-05-17/brief.md) | 71 | standalone | LLM-generated brief with planted hallucinations (fake McKinsey citation, fake competitors, fake regulatory framework) |
| [brief-quiet-social](briefs/brief-quiet-social-2026-05-17/brief.md) | 77 | standalone | Self-contradicting brief; six embedded contradictions |
| [brief-streetside-trees](briefs/brief-streetside-trees-2026-05-17/brief.md) | 81 | standalone | Standalone idea-stage brief (community urban tree mapping) |
| [brief-hivelink](briefs/brief-hivelink-2026-05-17/brief.md) | 111 | →hivelink | **Brief→PRD pair.** Maximalist enterprise altitude. |

### `prds/` — PRD-altitude specifications (53)

#### Baseline PRDs — pre-corpus (13)

| Entry | Lines | Domain | Notes |
|---|---|---|---|
| [prd-pomodorolite](prds/prd-pomodorolite-2026-05-17/prd.md) | 54 | productivity | Lean hobby weekend-build PRD. Has paired brief. *Demo candidate.* |
| [prd-vendorinsight](prds/prd-vendorinsight-2026-05-17/prd.md) | 72 | procurement | Clean baseline. Control case. |
| [prd-shipctl](prds/prd-shipctl-2026-05-17/prd.md) | 168 | devtools | Platform CLI replacing legacy bash script |
| [prd-echocraft](prds/prd-echocraft-2026-05-17/prd.md) | 184 | gaming | Browser game (asymmetric co-op puzzle). *Demo candidate.* |
| [prd-cardiacwatch](prds/prd-cardiacwatch-2026-05-17/prd.md) | 220 | medical research | Research protocol PRD (AFib prediction) |
| [prd-palletpulse](prds/prd-palletpulse-2026-05-17/prd.md) | 330 | IoT logistics | Industrial IoT with anchor customer |
| [prd-aurora-migration](prds/prd-aurora-migration-2026-05-17/prd.md) | 362 | backend infra | Rails monolith → microservices, PRD/RFC hybrid |
| [prd-craftcircle](prds/prd-craftcircle-2026-05-17/prd.md) | 404 | marketplace | Premium curated maker marketplace. *Demo candidate.* |
| [prd-plantsona](prds/prd-plantsona-2026-05-11/prd.md) | 491 | consumer social | Per-plant social network with AI voice. *Demo candidate.* |
| [prd-heart-rate-portal](prds/prd-heart-rate-portal-2026-05-15/prd.md) | 493 | medical (portfolio) | Portfolio web app, well-formed reference shape |
| [prd-medivault](prds/prd-medivault-2026-05-17/prd.md) | 723 | healthcare regulated | HIPAA records-exchange approval ceremony |
| [prd-hivelink](prds/prd-hivelink-2026-05-17/prd.md) | 890 | enterprise iPaaS | Maximalist 60+ FRs. Has paired brief. |
| [prd-narrative-vtt](prds/prd-narrative-vtt-2026-05-15/prd.md) | 11 | TBD | Pre-discovery stub; blocked-path test |

#### Posture variants — greenfield/brownfield/lifecycle (8)

| Entry | Lines | Posture |
|---|---|---|
| [prd-vendorinsight-csv-export](prds/prd-vendorinsight-csv-export-2026-05-17/prd.md) | 49 | Brownfield narrow feature-add |
| [prd-habit-share-extension](prds/prd-habit-share-extension-2026-05-17/prd.md) | 77 | Brownfield hobby extension |
| [prd-sunset-legacy-reports](prds/prd-sunset-legacy-reports-2026-05-17/prd.md) | 134 | Deprecation / sunset (inverse PRD) |
| [prd-oss-fork](prds/prd-oss-fork-2026-05-17/prd.md) | 147 | OSS fork with governance posture |
| [prd-pivot-b2c-to-b2b](prds/prd-pivot-b2c-to-b2b-2026-05-17/prd.md) | 160 | B2C → B2B pivot |
| [prd-soc2-retrofit](prds/prd-soc2-retrofit-2026-05-17/prd.md) | 163 | Compliance retrofit |
| [prd-scale-retrofit](prds/prd-scale-retrofit-2026-05-17/prd.md) | 184 | Scale-out fire-drill (12K → 180K MAU) |
| [prd-acquisition-integration](prds/prd-acquisition-integration-2026-05-17/prd.md) | 223 | M&A integration (Day 1 → Year 1) |

#### App-type variants — distribution and runtime (12)

| Entry | Lines | App type |
|---|---|---|
| [prd-watchos-reverse-alarm](prds/prd-watchos-reverse-alarm-2026-05-17/prd.md) | 92 | WatchOS app. *Demo candidate.* |
| [prd-telegram-bot-jorprice](prds/prd-telegram-bot-jorprice-2026-05-17/prd.md) | 100 | Telegram bot |
| [prd-email-newsletter-fielddispatch](prds/prd-email-newsletter-fielddispatch-2026-05-17/prd.md) | 101 | Email-as-product |
| [prd-browser-ext-tabtidy](prds/prd-browser-ext-tabtidy-2026-05-17/prd.md) | 104 | MV3 Chrome extension |
| [prd-vscode-ext-commitnudge](prds/prd-vscode-ext-commitnudge-2026-05-17/prd.md) | 110 | VS Code extension |
| [prd-print-kit-yearmark](prds/prd-print-kit-yearmark-2026-05-17/prd.md) | 113 | Print-on-demand physical kit |
| [prd-discord-bot-bookcorner](prds/prd-discord-bot-bookcorner-2026-05-17/prd.md) | 114 | Discord bot (slash commands) |
| [prd-mac-streamdeck-prompts](prds/prd-mac-streamdeck-prompts-2026-05-17/prd.md) | 130 | Native Mac menubar app. *Demo candidate.* |
| [prd-rag-obsidian-chat](prds/prd-rag-obsidian-chat-2026-05-17/prd.md) | 150 | RAG product (Obsidian plugin). *Demo candidate.* |
| [prd-ai-agent-inbox](prds/prd-ai-agent-inbox-2026-05-17/prd.md) | 179 | Pure AI agent product. *Demo candidate.* |
| [prd-oss-library-rateguard](prds/prd-oss-library-rateguard-2026-05-17/prd.md) | 203 | TypeScript library / SDK RFC |
| [prd-firmware-leakmate](prds/prd-firmware-leakmate-2026-05-17/prd.md) | 209 | Bare-metal MCU firmware |

#### Domain variants — real-world breadth (9)

| Entry | Lines | Domain |
|---|---|---|
| [prd-faith-quietroom](prds/prd-faith-quietroom-2026-05-17/prd.md) | 129 | Faith community |
| [prd-creator-daw-plugin-stemloop](prds/prd-creator-daw-plugin-stemloop-2026-05-17/prd.md) | 130 | Music creator (DAW plugin) |
| [prd-birding-warble](prds/prd-birding-warble-2026-05-17/prd.md) | 131 | Niche hobbyist (birding). *Demo candidate.* |
| [prd-pet-tech-greymuzzle](prds/prd-pet-tech-greymuzzle-2026-05-17/prd.md) | 140 | Pet tech (senior dogs). *Demo candidate.* |
| [prd-mental-health-anchor](prds/prd-mental-health-anchor-2026-05-17/prd.md) | 149 | Mental health (anxiety journaling) |
| [prd-civic-curbcut](prds/prd-civic-curbcut-2026-05-17/prd.md) | 156 | Civic tech / accessibility. *Demo candidate.* |
| [prd-edtech-mathmentor](prds/prd-edtech-mathmentor-2026-05-17/prd.md) | 160 | EdTech (regulated; AI tutor) |
| [prd-climate-carbon-tracker](prds/prd-climate-carbon-tracker-2026-05-17/prd.md) | 170 | Climate / ESG (B2B SaaS) |
| [prd-fintech-rentsplit](prds/prd-fintech-rentsplit-2026-05-17/prd.md) | 181 | Fintech (consumer payments) |

#### Edge / negative cases — deliberately problematic (4)

| Entry | Lines | Edge type |
|---|---|---|
| [prd-vibes-fashion-app](prds/prd-vibes-fashion-app-2026-05-17/prd.md) | 131 | Vibes-only PRD (brand-heavy, behavior-light) |
| [prd-spanish-mercado](prds/prd-spanish-mercado-2026-05-17/prd.md) | 133 | Full Spanish-language PRD |
| [prd-junior-pm-bad](prds/prd-junior-pm-bad-2026-05-17/prd.md) | 211 | Junior-PM PRD with 10 planted anti-patterns |
| [prd-engineering-heavy-cache](prds/prd-engineering-heavy-cache-2026-05-17/prd.md) | 312 | Engineering-only PRD (tech-heavy, user-light) |

#### Non-PRD shapes at PRD altitude (7)

| Entry | Lines | Shape |
|---|---|---|
| [prfaq-meetingdoctor](prds/prfaq-meetingdoctor-2026-05-17/prfaq.md) | 106 | Internal-tool PRFAQ; manager meeting-triage; HR-surveillance guardrails |
| [prfaq-internal-runbook-builder](prds/prfaq-internal-runbook-builder-2026-05-17/prfaq.md) | 108 | Internal-tool PRFAQ |
| [prfaq-rendezvous](prds/prfaq-rendezvous-2026-05-17/prfaq.md) | 130 | Consumer mobile PRFAQ; meet-in-the-middle weekend trip planner |
| [prfaq-snapboard](prds/prfaq-snapboard-2026-05-17/prfaq.md) | 131 | Amazon-style consumer PRFAQ |
| [jobstories-pantry](prds/jobstories-pantry-2026-05-17/jobstories.md) | 149 | Strict JTBD job-stories format |
| [prfaq-shipnow-launchpad](prds/prfaq-shipnow-launchpad-2026-05-17/prfaq.md) | 170 | Developer-platform PRFAQ; "repo to revenue in 30 minutes"; cannibalization/ICP questions |
| [rfc-oss-bigissue](prds/rfc-oss-bigissue-2026-05-17/rfc.md) | 287 | OSS RFC + emulated comment thread |

---

## Cross-cutting indexes

### Brief → PRD pairs (altitude-fidelity test set)

| Brief | PRD | What the pair tests |
|---|---|---|
| [brief-pomodorolite](briefs/brief-pomodorolite-2026-05-17/brief.md) (61L) | [prd-pomodorolite](prds/prd-pomodorolite-2026-05-17/prd.md) (54L) | Are the kernels equivalent at hobby altitude? |
| [brief-hivelink](briefs/brief-hivelink-2026-05-17/brief.md) (111L) | [prd-hivelink](prds/prd-hivelink-2026-05-17/prd.md) (890L) | Are the kernels equivalent at maximalist enterprise altitude? |

If kernels are equivalent across pairs, the brief carried the load-bearing signal and the PRD wrapper is ceremony for downstream purposes. If kernels diverge, the PRD altitude adds irreducible signal.

### By AI involvement

**AI-native products** (the product *is* AI-powered)
- [prd-ai-agent-inbox](prds/prd-ai-agent-inbox-2026-05-17/prd.md), [prd-rag-obsidian-chat](prds/prd-rag-obsidian-chat-2026-05-17/prd.md), [prd-edtech-mathmentor](prds/prd-edtech-mathmentor-2026-05-17/prd.md), [prd-birding-warble](prds/prd-birding-warble-2026-05-17/prd.md), [prd-creator-daw-plugin-stemloop](prds/prd-creator-daw-plugin-stemloop-2026-05-17/prd.md), [braindump-standup-killer](braindumps/braindump-standup-killer-2026-05-17/braindump.md)

**AI as primary feature**
- [prd-email-newsletter-fielddispatch](prds/prd-email-newsletter-fielddispatch-2026-05-17/prd.md), [prd-mac-streamdeck-prompts](prds/prd-mac-streamdeck-prompts-2026-05-17/prd.md), [prd-mental-health-anchor](prds/prd-mental-health-anchor-2026-05-17/prd.md) (bounded), [prd-faith-quietroom](prds/prd-faith-quietroom-2026-05-17/prd.md) (bounded), [prd-plantsona](prds/prd-plantsona-2026-05-11/prd.md), [braindumps/braindump-shelf-scanner](braindumps/braindump-shelf-scanner-2026-05-17/braindump.md), [transcripts/braindump-loom-transcript](transcripts/braindump-loom-transcript-2026-05-17/transcript.md), [prfaq-internal-runbook-builder](prds/prfaq-internal-runbook-builder-2026-05-17/prfaq.md), [prfaq-meetingdoctor](prds/prfaq-meetingdoctor-2026-05-17/prfaq.md), [prfaq-shipnow-launchpad](prds/prfaq-shipnow-launchpad-2026-05-17/prfaq.md)

**AI is the planted test signal** (LLM-generated artifacts with detectable issues)
- [brief-llm-fitness-platform](briefs/brief-llm-fitness-platform-2026-05-17/brief.md) (planted hallucinations), [brief-quiet-social](briefs/brief-quiet-social-2026-05-17/brief.md) (6 planted contradictions), [prd-junior-pm-bad](prds/prd-junior-pm-bad-2026-05-17/prd.md) (10 anti-patterns), all 4 `bmad-spec-kernel/kernel-violations-*` files

### Demo-worthy build-and-publish candidates

These are flagged as strongest BMad-Method-case-study candidates: charming enough to actually build, narrow enough to ship, audience-resonant.

- [prd-pomodorolite](prds/prd-pomodorolite-2026-05-17/prd.md) · iOS productivity, weekend-buildable
- [prd-plantsona](prds/prd-plantsona-2026-05-11/prd.md) · Rich strategic narrative for a category-creation product
- [prd-craftcircle](prds/prd-craftcircle-2026-05-17/prd.md) · Consumer brand story for a marketplace
- [prd-echocraft](prds/prd-echocraft-2026-05-17/prd.md) · Browser game (visually demo-able)
- [prd-ai-agent-inbox](prds/prd-ai-agent-inbox-2026-05-17/prd.md) · Claude-native; BMad audience resonance
- [prd-rag-obsidian-chat](prds/prd-rag-obsidian-chat-2026-05-17/prd.md) · Obsidian + Claude overlap with BMad audience
- [prd-mac-streamdeck-prompts](prds/prd-mac-streamdeck-prompts-2026-05-17/prd.md) · Mac-native, viral-friendly
- [prd-watchos-reverse-alarm](prds/prd-watchos-reverse-alarm-2026-05-17/prd.md) · Charming, narrow, ships fast
- [prd-birding-warble](prds/prd-birding-warble-2026-05-17/prd.md) · Niche-with-heart; ML + AR cool factor
- [prd-civic-curbcut](prds/prd-civic-curbcut-2026-05-17/prd.md) · Mission-driven; nonprofit case study
- [prd-pet-tech-greymuzzle](prds/prd-pet-tech-greymuzzle-2026-05-17/prd.md) · Emotional resonance + hardware story
- [braindump-standup-killer](braindumps/braindump-standup-killer-2026-05-17/braindump.md) · Founder-rant demo of bmad-spec extraction

### Spec Law test focus (for bmad-spec validation)

Entries deliberately stressing specific Spec Law rules:

- **Rule 1 (capability has both intent + success):** [intents/kernel-rss-revival](intents/kernel-rss-revival-2026-05-17/spec.md) (CAP-3 missing success)
- **Rule 2 (WHAT not HOW):** [intents/kernel-rss-revival](intents/kernel-rss-revival-2026-05-17/spec.md) (CAP-4), [prds/prd-aurora-migration](prds/prd-aurora-migration-2026-05-17/prd.md), [prds/prd-junior-pm-bad](prds/prd-junior-pm-bad-2026-05-17/prd.md), [prds/prd-shipctl](prds/prd-shipctl-2026-05-17/prd.md), [prds/prd-engineering-heavy-cache](prds/prd-engineering-heavy-cache-2026-05-17/prd.md), [bmad-spec-kernel/kernel-violations-vendor-soup](bmad-spec-kernel/kernel-violations-vendor-soup-2026-05-17/spec.md) (full-on vendor stuffing), [bmad-spec-kernel/kernel-violations-solution-disguised](bmad-spec-kernel/kernel-violations-solution-disguised-2026-05-17/spec.md) (UI patterns as caps)
- **Rule 3 (constraints actually bend design):** [intents/kernel-rss-revival](intents/kernel-rss-revival-2026-05-17/spec.md) (decorative constraint), [prds/prd-junior-pm-bad](prds/prd-junior-pm-bad-2026-05-17/prd.md); control cases: [prds/prd-palletpulse](prds/prd-palletpulse-2026-05-17/prd.md), [prds/prd-firmware-leakmate](prds/prd-firmware-leakmate-2026-05-17/prd.md), [bmad-spec-kernel/kernel-warehouse-pick-paths](bmad-spec-kernel/kernel-warehouse-pick-paths-2026-05-17/spec.md), [bmad-spec-kernel/kernel-insurance-claims-triage](bmad-spec-kernel/kernel-insurance-claims-triage-2026-05-17/spec.md)
- **Rule 4 (non-goals explicit):** [prds/prd-pomodorolite](prds/prd-pomodorolite-2026-05-17/prd.md) (absent), [prds/prd-junior-pm-bad](prds/prd-junior-pm-bad-2026-05-17/prd.md) (token only), [bmad-spec-kernel/kernel-violations-mixed-concerns](bmad-spec-kernel/kernel-violations-mixed-concerns-2026-05-17/spec.md) (non-goals smuggle aspirations)
- **Rule 5 (success signal concrete):** [intents/kernel-rss-revival](intents/kernel-rss-revival-2026-05-17/spec.md), [prds/prd-echocraft](prds/prd-echocraft-2026-05-17/prd.md) ("delighted gasp"), [prds/prd-craftcircle](prds/prd-craftcircle-2026-05-17/prd.md), [prds/prd-junior-pm-bad](prds/prd-junior-pm-bad-2026-05-17/prd.md), [prds/prd-vibes-fashion-app](prds/prd-vibes-fashion-app-2026-05-17/prd.md), [bmad-spec-kernel/kernel-violations-non-falsifiable](bmad-spec-kernel/kernel-violations-non-falsifiable-2026-05-17/spec.md), [bmad-spec-kernel/kernel-violations-mixed-concerns](bmad-spec-kernel/kernel-violations-mixed-concerns-2026-05-17/spec.md) (success is a laundry list)

### BMad-bmb reroute candidates

Some entries describe a "product" that, on inspection, is really a request for a BMad agent, skill, or workflow. A well-built bmad-spec or routing skill should flag these for the BMad Builder (`bmad-bmb`) instead of marching them down the PRD pipeline. These entries are the routing-decision test set.

**Strong reroute (the described product *is* a BMad agent/skill/workflow):**

| Entry | Why it reroutes |
|---|---|
| [braindumps/braindump-standup-killer](braindumps/braindump-standup-killer-2026-05-17/braindump.md) | An AI agent ingesting dev signals (commits, PRs, calendar) and emitting an async standup. BMad agent + workflow, not a SaaS. |
| [prds/prfaq-meetingdoctor](prds/prfaq-meetingdoctor-2026-05-17/prfaq.md) | Calendar/Zoom-metadata ingest → meeting recommendations. BMad workflow: ingest skill + analysis skill + report skill. |
| [prds/prfaq-internal-runbook-builder](prds/prfaq-internal-runbook-builder-2026-05-17/prfaq.md) | Generate runbooks from incident artifacts. Textbook BMad skill, or small workflow chaining ingest → outline → fill. |
| [braindumps/braindump-after-customer-coffee](braindumps/braindump-after-customer-coffee-2026-05-17/braindump.md) | Founder explicitly wants help structuring post-coffee customer-signal interpretation. BMad skill ("post-customer-conversation analysis"). |
| [braindumps/braindump-post-call-memo](braindumps/braindump-post-call-memo-2026-05-17/memo.md) | "Help me distill this hot customer call before I lose the thread." BMad skill. |
| [transcripts/transcript-zoom-customer-interview](transcripts/transcript-zoom-customer-interview-2026-05-17/transcript.md) | Magic-wand answer is "something that listens to conversations and generates the handoff as a byproduct" — transcript-ingest skill + structured-summary skill, not a SaaS. |

**Ambiguous overlap (could be a BMad skill or a real product depending on goal):**

| Entry | The tension |
|---|---|
| [prds/prd-ai-agent-inbox](prds/prd-ai-agent-inbox-2026-05-17/prd.md) | Claude-native inbox triager. BMad agent if the user owns the runtime; SaaS if they want to sell it to non-BMad users. |
| [prds/prd-rag-obsidian-chat](prds/prd-rag-obsidian-chat-2026-05-17/prd.md) | Obsidian RAG chat is genuinely a BMad-installable skill for many users. Routing depends on whether the goal is a community Obsidian plugin or a personal BMad knowledge agent. |
| [prds/prd-mac-streamdeck-prompts](prds/prd-mac-streamdeck-prompts-2026-05-17/prd.md) | Stream Deck → AI prompts is one BMad-skill thin client away from existing. |
| [prds/prd-vscode-ext-commitnudge](prds/prd-vscode-ext-commitnudge-2026-05-17/prd.md) | Commit-message nudging is a BMad developer skill in disguise; the IDE-extension wrapper is the only product-y part. |

**Stay as-is (genuinely product, SaaS, hardware, or regulated — not BMad work):**

All BRDs (corporate ops systems), `prd-fintech-rentsplit`, `prd-edtech-mathmentor`, `prd-medivault`, `prd-cardiacwatch`, `prd-firmware-leakmate`, `prd-palletpulse`, `prd-watchos-reverse-alarm`, the games/marketplaces (`prd-echocraft`, `prd-craftcircle`), and most consumer-mobile PRFAQs (`prfaq-rendezvous`) describe products that need to ship as products. A router should NOT reroute these.

**As a test set:** 6 strong reroute + 4 ambiguous + the broader "stay as-is" set is a graded difficulty curve for any BMad routing or triage skill. Misroutes in either direction are calibration signals.

### BRD-specific test focus

BRDs are deliberately product-light. They test whether downstream skills can ask the right product questions to recover what the BRD omits.

- **Vague-requirement detection:** [brds/brd-support-knowledge-base](brds/brd-support-knowledge-base-2026-05-17/brd.md) (3 obviously-vague FRs), [brds/brd-procurement-vendor-portal](brds/brd-procurement-vendor-portal-2026-05-17/brd.md) (FR-016/FR-020 "user-friendly")
- **Internal contradiction detection:** [brds/brd-hr-onboarding-revamp](brds/brd-hr-onboarding-revamp-2026-05-17/brd.md) (FR-005 vs FR-009)
- **Vendor-as-requirement detection:** [brds/brd-sales-quote-generator](brds/brd-sales-quote-generator-2026-05-17/brd.md) (FR-011 DocuSign), [brds/brd-finance-month-end-close](brds/brd-finance-month-end-close-2026-05-17/brd.md) (FR-013 Blackline)
- **Impossible/wish requirement detection:** [brds/brd-compliance-audit-trail](brds/brd-compliance-audit-trail-2026-05-17/brd.md) (FR-014 retroactive backfill), [brds/brd-finance-month-end-close](brds/brd-finance-month-end-close-2026-05-17/brd.md) (FR-018 "continuous close")
- **Gap detection (what's missing):** all 6 BRDs omit user journeys; [brds/brd-support-knowledge-base](brds/brd-support-knowledge-base-2026-05-17/brd.md) explicitly omits authoring incentives and staleness detection

### By density / weight class

- **Tweet (1-10L):** [intents/intent-podcast-sync](intents/intent-podcast-sync-2026-05-17/intent.md) (3), [intents/intent-tweet-i-want-to-build](intents/intent-tweet-i-want-to-build-2026-05-17/intent.md) (3), [intents/intent-three-line-pitch](intents/intent-three-line-pitch-2026-05-17/intent.md) (3)
- **Sparse (10-50L):** [prds/prd-narrative-vtt](prds/prd-narrative-vtt-2026-05-15/prd.md) (11), [intents/intent-text-to-cofounder](intents/intent-text-to-cofounder-2026-05-17/intent.md) (13), [intents/bullets-inventory-scanner](intents/bullets-inventory-scanner-2026-05-17/requirements.md) (19), [braindumps/braindump-shower-thought-monetization](braindumps/braindump-shower-thought-monetization-2026-05-17/braindump.md) (29), [briefs/brief-coffeebean-tracker](briefs/brief-coffeebean-tracker-2026-05-17/brief.md) (31), all 4 clean `bmad-spec-kernel/kernel-*` exemplars (31-33), [prds/prd-pomodorolite](prds/prd-pomodorolite-2026-05-17/prd.md) (54), [prds/prd-vendorinsight-csv-export](prds/prd-vendorinsight-csv-export-2026-05-17/prd.md) (49)
- **Lean (50-100L):** ~14 entries including the violation kernels (38-47L), intent-readme-stub (68), brief-pomodorolite, brief-streetside-trees, brief-quiet-social, brief-llm-fitness-platform, vendorinsight, prd-habit-share-extension, prd-watchos-reverse-alarm, braindumps/braindump-shelf-scanner, braindumps/braindump-after-customer-coffee, braindumps/braindump-conference-hallway-idea, braindumps/braindump-post-call-memo, brd-support-knowledge-base
- **Standard (100-200L):** ~45 entries — the meat of the corpus; includes all PRFAQs, most domain PRDs, most BRDs, and most threads
- **Mid-heavy (200-400L):** ~16 entries including prd-junior-pm-bad, research/synthesis-customer-interviews, prd-cardiacwatch, prd-acquisition-integration, threads/thread-email-roi-tool, threads/thread-slack-pricing-debate, threads/thread-github-issue-discussion, threads/thread-email-cancellation-feature, braindumps/braindump-discord-paste, prds/rfc-oss-bigissue, prd-engineering-heavy-cache, prd-palletpulse, prd-aurora-migration, prd-craftcircle, transcripts/transcript-zoom-customer-interview, transcripts/transcript-walking-meeting
- **Maximalist (500-1000L):** prd-heart-rate-portal (493), prd-plantsona (491), prd-medivault (723), prd-hivelink (890)

---

## Experiments the corpus enables

### 1 · Universal-distiller validation

For each non-PRD-shaped entry (`braindumps/`, `transcripts/`, `threads/`, `intents/`, `research/`, `brds/`), distill via bmad-spec and verify a workable five-field kernel comes out. If extraction holds across all shapes, the universal-distiller thesis is supported.

### 2 · Kernel-enough hypothesis

For each brief→PRD pair in `briefs/`, distill both sides and compare kernels.
- **Equivalent kernels** → the brief carried the load-bearing signal; the PRD wrapper is ceremony for downstream purposes.
- **Divergent kernels** → the PRD altitude adds irreducible signal.

Extension: hand the distilled kernel (no source) to bmad-create-architecture, bmad-create-ux, bmad-create-epics-and-stories. Measure downstream output quality vs. handing them the full PRD. This is the falsification test for "is the kernel enough downstream?"

### 3 · Spec Law detection rigor

For each entry with planted violations ([intents/kernel-rss-revival](intents/kernel-rss-revival-2026-05-17/spec.md), [briefs/brief-quiet-social](briefs/brief-quiet-social-2026-05-17/brief.md), [briefs/brief-llm-fitness-platform](briefs/brief-llm-fitness-platform-2026-05-17/brief.md), [prds/prd-junior-pm-bad](prds/prd-junior-pm-bad-2026-05-17/prd.md), [prds/prd-vibes-fashion-app](prds/prd-vibes-fashion-app-2026-05-17/prd.md), [prds/prd-engineering-heavy-cache](prds/prd-engineering-heavy-cache-2026-05-17/prd.md), and all 4 `bmad-spec-kernel/kernel-violations-*`), run bmad-spec and verify it surfaces the planted issues as direct findings, assumptions, or open questions. Misses are calibration signals.

### 4 · Density floor

For the thinnest entries (the 3-line intents, [intents/bullets-inventory-scanner](intents/bullets-inventory-scanner-2026-05-17/requirements.md), [braindumps/braindump-shower-thought-monetization](braindumps/braindump-shower-thought-monetization-2026-05-17/braindump.md)), verify bmad-spec produces appropriate output — `blocked` for genuinely-too-thin, `partial` with extensive open-questions for sparse-but-distillable, `complete` for the bullet-list.

### 5 · Language and idiom robustness

[prds/prd-spanish-mercado](prds/prd-spanish-mercado-2026-05-17/prd.md) tests extraction without English idioms. The kernel should be functionally equivalent to what a parallel English-rewrite would produce.

### 6 · Demo flywheel

For each demo-worthy candidate, the entry is the *starting input*. Run the full BMad chain (spec → architecture → UX → epics → dev) and publish the transcript as a BMad case study. The chain trail becomes the demonstration of the method.

### 7 · BRD-to-product translation

For each entry in `brds/`, run bmad-product-brief or bmad-create-prd and measure how well the resulting product artifact:
- recovers user journeys the BRD omitted
- challenges vendor-as-requirement smuggling
- flags contradictions and impossible asks
- exposes the unstated assumptions (who maintains it, what success looks like for the end user, not just the stakeholder)

This is the "is BMad better than a typical IT-receives-BRD handoff?" test.

---

## Conventions

- Entries live in date-stamped folders (`{slug}-YYYY-MM-DD/`) so multiple revisions of the same product can coexist over time.
- Each folder typically contains a single primary artifact (`prd.md`, `brief.md`, `brd.md`, `spec.md`, `voicememo.md`, `thread.md`, `transcript.md`, `braindump.md`, etc.) and optionally a `.decision-log.md` or `spec.md` sibling.
- Product names are deliberately fictional; nothing in this repo represents real shipped products.
- All artifacts are hand-shaped to test a specific thing. Avoid filler. Avoid generic samples that don't exercise an edge case.

## Contributing

When adding a new entry:
1. Pick the folder that matches the artifact's shape (or open a discussion to add a folder).
2. Use the `{slug}-YYYY-MM-DD/` directory convention with a primary-artifact filename inside.
3. Add a row to the relevant section of this index, including the test signal the entry carries.
4. If you plant intentional violations or contradictions for testing, note them in the entry's notes column so they remain rediscoverable.
