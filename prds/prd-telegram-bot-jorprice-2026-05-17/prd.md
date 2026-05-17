# JorPrice: Telegram bot for real-USD price conversion in Argentina

**Owner:** Joaquín "Jor" Pereyra (solo dev, Buenos Aires)
**Status:** Draft v0.1
**Date:** 2026-05-17
**Platform:** Telegram Bot API (webhook mode)

## Problem

Argentine shoppers comparing imported goods (electronics, kitchen equipment, tools, hobby gear) cannot trust the sticker price in ARS. The "real" purchasing-power conversion depends on which dollar you use: oficial, blue, MEP, or cripto. Today people copy the ARS number into a calculator, open a separate tab on ámbito.com or dolarhoy.com, do the math, and lose context of the conversation. The friction is highest in the place the conversation actually happens, which in Argentina is Telegram (shopping channels, family groups, reseller chats).

## Target user

Primary: Argentine consumers aged 22 to 55 active in Telegram shopping channels and price-comparison groups (Capital Federal, GBA, Córdoba, Rosario, Mendoza). Secondary: adjacent markets where dual exchange rates matter and Telegram penetration is high, specifically Venezuela, Cuba (diaspora-facing channels), and to a smaller degree Uruguay and Chile (stretch). Not aimed at traders or arbitrageurs.

## Functional requirements

1. Quote current dollar rates on demand and show 24h delta per rate type.
2. Convert an ARS amount to USD using the user's chosen reference rate (default: blue).
3. Let users register price alerts that fire when the rate moves enough to cross a USD threshold for a specific ARS sticker price.
4. Work in-line from any chat without the user opening the bot's DM.
5. Optional: format prices inside channels where the bot is admin (pin-message reformatting).
6. Cache rates for 60 seconds across all users to respect upstream sources.
7. Spanish-first copy. English command aliases recognized but not advertised.

## Bot surface

### Commands (private chat and direct mentions in groups)

- `/start` Onboarding. Sets default reference rate via inline keyboard (Blue / MEP / Oficial / Cripto).
- `/dolar` Returns all four rates with 24h delta. Example: `Blue 1.245 (+1,2%) | MEP 1.198 (+0,8%) | Oficial 987 (0,0%) | Cripto 1.260 (+1,5%)`.
- `/precio <ARS>` Converts using the user's saved reference rate. Reply includes a "ver en otras cotizaciones" inline button that expands to all four.
- `/seguir <ARS> <umbral_USD>` Creates an alert. Bot DMs the user when the conversion crosses the threshold in either direction.
- `/misalertas` Lists active alerts with inline "borrar" buttons (callback queries).
- `/ref` Re-opens the reference-rate picker.
- `/about` Credits, data sources, donation link (Cafecito, the local Patreon equivalent).

### Inline mode

Typing `@jorprice 549000` from any chat (group, channel comment, DM with a friend) produces inline results:

- `USD 432 (blue)` tap-to-insert
- `USD 401 (MEP)` tap-to-insert
- `USD 556 (oficial)` tap-to-insert

Inline results are cached 60s per ARS amount. The "via @jorprice" attribution stays visible (Telegram default) which doubles as organic distribution.

### Callback queries

Used for: reference-rate picker, alert threshold quick-picks (USD 50 / 100 / 250 / 500 / custom), and alert deletion confirmations. All callback handlers acknowledge within 3 seconds to avoid the Telegram "loading" spinner timing out.

### Channel mode (optional, opt-in)

When added as channel admin with `post_messages` permission, the bot watches new posts. If a post contains a recognizable ARS price pattern (`$ 549.000`, `ARS 549000`, `pesos 549k`), the bot edits or replies with a one-line USD breakdown. Channel owner can disable per-channel with `/jorprice off`.

### Payments

No Telegram Payments integration in v1. Donations are routed to Cafecito via a link in `/about`. Telegram Payments (Stripe / Yandex provider tokens) is a v2 question, not a v1 commitment.

## Webhook vs. long-polling

Webhook. Long polling burns idle compute and adds latency at scale. The bot will run on a single small VPS (Hetzner CX22, Buenos Aires latency acceptable from Falkenstein) behind an nginx TLS terminator, with the Telegram webhook pointed at `https://jorprice.ar/tg/<secret_path>`. Long polling stays available as a local-dev fallback toggled by env var.

## Constraints

- Telegram Bot API rate limits: 30 messages/sec total outbound, 20 messages/minute per group chat. Alert fan-out must batch and throttle.
- Webhook handler must respond in under 800ms p95. Telegram retries on timeout, which would double-fire alerts. Heavy work (rate fetch, alert eval) runs in a worker queue, not in the webhook handler.
- Rate data: ámbito.com and dolarhoy.com via responsible scraping (User-Agent identifies the bot, 60s cache, exponential backoff on 429). Fallback: bluelytics.com.ar public API.
- Spanish-first copy. Locale detection via Telegram `language_code` field but defaults to es-AR when ambiguous.
- Group privacy mode ON (BotFather `/setprivacy` enabled). Bot only receives messages that mention it or are commands, unless explicitly made admin. Prevents the "your bot is reading our chat" objection.
- No `message_content` permission requested in groups. Channel admin role is opt-in per channel.
- GDPR-style data minimization: store only Telegram user_id, chosen reference rate, and active alerts. No message history. No analytics SDK.

## Non-goals

- Not a trading product. No buy / sell signals, no portfolio tracking.
- No integrations with banks, brokers, or exchanges. Display only, no transaction surface.
- No fiat-to-fiat for currencies outside USD/ARS in v1. USD/UYU and USD/CLP are stretch for v1.1.
- No web companion app in v1. The bot is the product.
- No paid tier in v1. Donations only.
- No WhatsApp port. WhatsApp Business API is a separate, expensive effort with different UX primitives.

## Success signal

Within 90 days of public launch:

- 5.000 unique users have run `/dolar` or `/precio` at least once.
- 25% of users have set a default reference rate (proxy for repeat use).
- 500 inline queries per day at the 30-day mark (signals viral surface working, since inline mode runs in chats the bot is not part of).
- Median webhook handler response under 400ms.
- Zero rate-limit-induced retries from Telegram over a rolling 7-day window.

## Open questions

1. Channel mode default: opt-in per channel admin, or off everywhere until the admin types `/jorprice on`? Leaning opt-in to avoid surprise edits.
2. Alert eviction: how long do we keep an alert that has fired once? Keep until user deletes, or auto-expire after 30 days?
3. Cripto rate source: which exchange's USDT/ARS do we treat as canonical? Buenbit, Lemon, or a blended index?
4. Stretch markets (UY, CL): do we ship as separate bots (`@jorprice_uy`) or one bot with a `/pais` switch? Separate bots is simpler for v1.1.
5. Cafecito vs. Mercado Pago link for donations: Cafecito has the cleaner per-coffee UX, MP has higher recognition. Probably both.
6. Do we expose a read-only HTTP endpoint for the cached rates so other community bots can consume them without re-scraping? Cheap to offer, possible goodwill.
