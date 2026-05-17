# Spec: Creator Tipping App

## Problem

Independent creators (writers, streamers, podcasters, illustrators) struggle to capture small, spontaneous appreciation payments from their audience. Existing platforms take large cuts, require account creation by the tipper, or feel transactional rather than personal. Creators want a frictionless way for fans to send small tips with a short message, and fans want to feel their support actually reached the creator.

## Capabilities

- Use Stripe Connect Express accounts for creator onboarding and payouts
- Leverage AWS Lambda functions behind API Gateway for tip ingestion
- Send tip-received notifications to creators via Twilio SMS and SendGrid email
- Store tip messages and creator profile embeddings in Pinecone for similarity search
- Authenticate creators via Auth0 with social login providers enabled
- Build the web app using Next.js App Router with server actions for all mutations
- Run product analytics through Mixpanel with Segment as the CDP layer
- Host creator profile pages on Vercel with ISR revalidation every 60 seconds
- Use Supabase Postgres with Row Level Security for the tips ledger
- Cache leaderboard queries in Upstash Redis with a 5 minute TTL
- Process fraud signals through Sift Science before settling payouts
- Generate creator thank-you replies using the OpenAI Assistants API
- Deliver in-app push notifications via OneSignal across iOS and Android
- Render the mobile client in React Native with Expo EAS for builds

## Constraints

- Tippers must be able to send a tip without creating an account
- Minimum tip amount is $1, maximum single tip is $500
- Platform fee must remain under 5% inclusive of payment processing
- Creators must receive funds within 2 business days of a tip clearing
- All tip messages must be moderated before the creator sees them
- The product must comply with US money transmission requirements in launch states
- Personally identifiable information about tippers must not be shared with creators beyond an optional display name

## Non-goals

- Subscriptions, memberships, or recurring patronage
- Marketplace features (commissions, requests, paid DMs)
- Tipping in cryptocurrency or non-USD currencies at launch
- Creator-to-creator transfers or splits
- A discovery feed or social graph between creators

## Success signal

Within 90 days of launch, at least 40% of onboarded creators receive a tip in their first 14 days, median time from tip submission to creator notification is under 30 seconds, and chargebacks plus refunds stay under 1.5% of gross tip volume.

---
Annotation: planted Spec Law violations — vendor/stack names disguised as capabilities.
