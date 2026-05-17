# ShipNow Launchpad PRFAQ

Date: May 17, 2026
Status: Draft for internal review
Owner: Product, ShipNow Platform
Working-Backwards artifact. Not a public document until launch sign-off.

---

## Press Release

### ShipNow Launches Launchpad: From Repo to Revenue in 30 Minutes

SAN FRANCISCO, May 17, 2026. ShipNow today announced ShipNow Launchpad, a new product that takes a half-finished side project sitting in a GitHub repo and turns it into a live, billable business on a custom domain in under 30 minutes. Launchpad wires up authentication, payments, a feedback widget, and product analytics automatically, without the developer writing any infrastructure code, any Terraform, or any glue logic between vendors. The product is generally available today for individual developers, with team and company tiers rolling out through the third quarter.

Launchpad is built for the developer who already has the hard part done. The code works on their laptop. The feature is real. What is missing is the boring middle: a domain, TLS, login, a paywall, a database that will not fall over, a way to know if anyone is using it, and a way to hear from them when something breaks. Launchpad treats that boring middle as a single managed surface. A developer connects a GitHub repo, answers a short interview about what the app does and who pays for it, and Launchpad produces a running product at a real URL, with a real Stripe account attached, and a real signup flow that real customers can complete.

Under the hood, Launchpad is an opinionated layer on top of the existing ShipNow PaaS. It uses the same build system, the same runtime, and the same regional infrastructure that already runs production workloads for tens of thousands of ShipNow customers. What is new is the orchestration. Launchpad detects the framework, provisions the data layer, attaches managed auth and managed billing, deploys a customer-facing site and an admin console, and gives the developer a single dashboard that shows revenue, signups, errors, and inbound feedback side by side. When the project outgrows the Launchpad defaults, it graduates onto the standard ShipNow platform without a rewrite.

"Most side projects do not die because the code is bad. They die because the developer hits the wall between code and a business and decides it is not worth the weekend," said Priya Annamalai, CEO of ShipNow.

"Launchpad removes that wall. If you have a repo and an idea of who would pay you, you can have a live product taking payments before lunch. That is not a demo. That is the actual product. We think this changes what a side project is allowed to become," Annamalai added.

The company has been quietly testing Launchpad with a few hundred developers for the past five months. During that closed beta, the median time from connecting a repo to taking a first real customer payment was 41 hours, and more than one third of projects had a paying customer within the first week. The company says this is the metric it cares about most. Deploys are easy to count. Revenue events are not.

Early access users have already shipped real businesses on Launchpad during the closed beta. "I had a half-built scheduling tool for music teachers sitting in a private repo for fourteen months," said Devon Park, an indie developer based in Austin. "I pointed Launchpad at it on a Saturday morning. By that night I had three paying teachers, a custom domain, a working login, and a Stripe payout scheduled. I did not touch a config file. I did not pick a database. I did not write a single line of auth code. I just kept building the part of the product that was actually mine."

Park said the project, now called LessonRail, has grown to 62 paying music teachers across the United States and Canada, generating enough monthly revenue to cover his rent. He still has a day job. He says Launchpad lowered the cost of finding out whether the idea was real, and that was the part he had been stuck on for over a year.

ShipNow Launchpad is available today at shipnow.dev/launchpad. Pricing starts at a flat monthly platform fee plus a small percentage of revenue processed through the integrated billing layer, with no separate charges for compute, bandwidth, or managed services until the project crosses defined growth thresholds. Existing ShipNow customers can enable Launchpad on any eligible repo from their dashboard.

---

## Customer FAQ

### What exactly does Launchpad do that the base ShipNow PaaS does not?

The base PaaS gives you a runtime, a build pipeline, and a deploy target. You still have to choose, wire, and pay for auth, billing, analytics, a database, a domain, and a feedback path. Launchpad assembles all of that for you, picks defaults that work for a brand-new product, and gives you one dashboard that shows business metrics next to system metrics. It is a productized opinion on top of the same platform. Think of the PaaS as the engine and Launchpad as the entire car, with the dashboard, the seats, and the keys already in your hand.

### How long does it actually take, and what does that 30-minute number assume?

For a typical Node, Python, or Go web app with a working local dev experience, end-to-end setup is usually 12 to 25 minutes. The 30-minute claim assumes you stop to answer the interview thoughtfully and connect your own Stripe and domain. If you let Launchpad use a temporary subdomain and a sandbox payments account, you can be live in under 10. The claim does not assume your repo is in any particular state of polish. It assumes the app builds and runs locally.

### What languages, frameworks, and custom stacks are supported?

At launch: Node.js (Next.js, Remix, Express, Hono, NestJS), Python (FastAPI, Django, Flask), Go (net/http, Echo, Chi), Ruby on Rails, and Elixir Phoenix. Static frontends paired with any of the above are supported automatically. Rust (Axum) and Java (Spring Boot) are in private preview and will move to general availability in the next quarter.

If your stack is not on that list, Launchpad still runs anything that builds in a Dockerfile. You lose some of the auto-detected scaffolding (the framework-specific auth glue and the typed billing helpers) and fall back to a generic adapter. You still get the managed domain, TLS, billing, analytics, and feedback widget. Most teams with a custom stack land here and report it is still a major time savings.

### What data leaves my repo, and what is the lock-in story?

The repo contents are cloned into the same build infrastructure that already runs ShipNow CI. Source code is not used to train any model. The interview answers (what the app does, who pays for it, what to call the product) are stored in your project metadata and used only to configure your environment. We do not send your source code, your environment variables, or your customer data to third-party LLM providers. There is a detailed data-flow diagram in the security docs.

On lock-in, Launchpad uses standard, portable building blocks. Your code stays in your GitHub repo. Your customer data lives in a standard Postgres database you can dump at any time. Your billing relationship is directly with Stripe under your own account, not resold through us. Your domain is registered in your name, not ours. If you leave, you take all of that with you. The thing you lose is the orchestration layer, which is the part you were paying us for in the first place.

### Can I move off Launchpad later without a rewrite?

Yes. There is an explicit "graduate" path that moves a Launchpad project onto the standard ShipNow PaaS. You keep the same runtime, the same database, the same domain, and the same customers. What changes is that you take direct ownership of the configuration that Launchpad was managing for you. Most projects graduate when they want to customize something Launchpad does not expose, or when they hit a team size where the opinions stop fitting. Graduation is a one-click operation followed by a guided checklist. We do not require a re-deploy and we do not break sessions.

### Does this replace my CI?

No. Launchpad uses your existing GitHub Actions or other CI for tests and checks. It only takes over the deploy step and the provisioning of attached services. If you do not have CI yet, Launchpad will generate a starter workflow, but you own it and can edit it like any other workflow file in your repo. This is deliberate. We do not want to be the thing that decides whether your tests pass. We want to be the thing that takes a passing build and turns it into a running business.

### What does it cost, and what happens when I outgrow the defaults?

Pricing has two components. First, a flat platform fee per active project per month, which covers the runtime, the database, the managed auth, the analytics, and the feedback widget up to defined usage thresholds. Second, Launchpad takes a small percentage of revenue processed through the integrated billing layer, capped per project per month. There are no separate line items for bandwidth, build minutes, or seats while you are on Launchpad.

When a project crosses a threshold (sustained traffic above a defined request-per-second band, or a database working set above a defined size), Launchpad notifies the owner and offers two options: stay on Launchpad and move to a metered tier, or graduate to the standard PaaS. Nothing breaks at the threshold. We do not throttle a paying product without consent. Detailed pricing is on the pricing page.

### Do you host my Stripe account or is it really mine?

It is really yours. Launchpad uses Stripe Connect in standard mode for the integrated billing layer. Your business, your tax ID, your payouts, your customers, your refunds. We never custody your revenue. The percentage we take is invoiced to you, not deducted from payouts. If you ever decide to leave Launchpad, your Stripe relationship continues uninterrupted and your customers do not see a billing change.

### Can I bring my own auth, analytics, or feedback tools?

The defaults are designed to be useful on day one and replaceable on day thirty. Default managed auth supports email, magic link, Google, GitHub, and Apple. You can swap in Auth0, WorkOS, Clerk, Cognito, or a self-hosted Keycloak through a documented adapter. The bundled analytics can be disabled and pointed at PostHog, Mixpanel, Amplitude, or a self-hosted alternative. The feedback widget can be turned off or replaced. Swapping any one of these does not affect the others.

### Is this safe to use for something that handles real customer data?

Yes for general consumer and B2B SaaS data. The platform is SOC 2 Type II and inherits the existing ShipNow compliance posture, including encryption at rest and in transit, isolated tenant databases by default, and audit logging on platform actions. For regulated workloads (PHI, cardholder data beyond what Stripe handles for you, regulated financial data), Launchpad is not the right starting surface today. Those workloads should start on the standard PaaS or on the enterprise tier, where the compliance scope is broader and the configurability is higher.

### Can I use Launchpad for a client project, and what if my project is not a web app?

Client projects are fine with the caveat that the project is owned by whoever owns the GitHub repo, the Stripe account, and the domain. There is no separate agency billing surface yet, so handoffs to a client mean transferring those three things. That is a known gap on the roadmap. As for non-web projects, Launchpad at launch is opinionated about web products with a frontend, a backend, and paying users. Pure CLIs, pure libraries, ML training jobs, and game servers are out of scope today. An API-only variant that drops the frontend assumptions is being evaluated but is not committed.

---

## Internal / Strategy FAQ

### Does Launchpad cannibalize the existing ShipNow PaaS plan?

Partially, and intentionally.

About 18 percent of current PaaS customers on the entry tier are using a small fraction of the platform and would be better served by Launchpad. We expect roughly that share to migrate in the first two quarters.

Average revenue per account on Launchpad is modeled higher than the entry PaaS tier because of the revenue-share component, so the migration is net positive on revenue per account.

The bigger story is the new top-of-funnel: developers who would never have started on the PaaS at all because the activation cost was too high. Modeling suggests Launchpad expands our addressable signup audience by roughly four times within twelve months, with the largest gains in regions where ShipNow has historically underperformed against runtime-only competitors.

### Who is the ICP?

Three concentric circles.

Primary: the indie developer or small founding team with a working prototype and a clear hypothesis about who would pay them. They have shipped code before. They do not want to spend their weekend wiring Stripe to Postgres to a domain registrar. This is the audience the product is designed around and the audience the closed beta validated.

Secondary: the developer inside a larger company spinning up an internal or skunkworks product who needs to get to a working URL with billing for a real internal customer. They are unblocking themselves around a slow platform team.

Tertiary, and explicitly not a launch target: large teams running production workloads. Those belong on the standard PaaS or enterprise tier. If they land on Launchpad first, the graduation path takes them where they should have started.

### Why is this not a head-on attack on Vercel, Render, Fly, or Railway?

Those products compete on runtime and developer experience for the deploy step. Launchpad is not selling a better deploy step.

It is selling the orchestration of the business surface around the deploy step: auth, billing, analytics, domain, feedback, observability, all wired together with opinions. A developer can still choose any of those platforms as their underlying runtime in the future.

The category we are creating is "repo to revenue," not "repo to URL."

We expect competitive responses, and we expect our durable advantage to be the integrated billing relationship and the graduation path into a real PaaS. The billing relationship is structurally hard to copy without a deep Stripe partnership and a willingness to take revenue share instead of seat revenue, which is a different financial model than the runtime competitors are organized around.

### What is the trojan-horse business move?

Launchpad is a low-friction front door to the ShipNow platform. The first surface a developer touches is Launchpad.

As their project grows, three things happen in order. First, they invite a teammate (seat expansion). Second, they cross a usage threshold (tier expansion). Third, they graduate to the standard PaaS (product expansion).

The PaaS is the long-term home. Launchpad is how we get the right customers there without making them shop for a PaaS first.

The revenue share on integrated billing also gives us a direct economic interest in our customers succeeding, which is a stronger alignment than per-seat or per-compute pricing on its own. It also gives the sales team a reason to talk to customers about growth rather than usage, which changes the texture of the relationship.

### What is the path to enterprise?

Two paths. First, the graduation path: a Launchpad project that grows into a real company eventually needs SSO, audit logs, role-based access, regional residency, and procurement-friendly contracts. That is the standard upsell motion into the existing enterprise tier. Second, the inside-the-enterprise path: large companies have hundreds of internal half-finished projects today and no good way to let an internal team stand one up without a six-week platform-team ticket. A "Launchpad for Enterprise" variant lets a platform team publish their own opinionated Launchpad inside the company, with their own approved building blocks. That is on the roadmap for the following year and is the larger long-term prize.

### How do we keep Launchpad from becoming a junk drawer of vibe-coded SaaS?

Three mechanisms.

First, the platform fee is non-zero from day one, which filters out projects that are not even worth a few dollars a month to the owner. A free tier would invite exactly the inventory we do not want.

Second, idle projects are automatically suspended after a defined inactivity window, with a clear restore path, so the platform is not paying to host abandoned demos forever. The default window is 30 days of no traffic and no deploys.

Third, the integrated billing share is the main revenue driver, which means our incentives are aligned with projects that have actual paying customers, not projects that exist only as portfolio pieces. We are explicitly not trying to be the cheapest place to host a never-launched idea. We are trying to be the highest-conversion place to find out if an idea is real.

### What is the launch risk we are most worried about?

The "it worked on the demo, it does not work on my repo" failure. Real repos have weird build steps, undocumented env vars, and assumptions about the local filesystem. Our internal target is a 90 percent successful first-deploy rate across a curated sample of public open-source web apps, and we are not at that number yet on the long tail. The mitigation is a strong fallback to a Dockerfile path and an explicit, friendly "we could not auto-detect this, here is what to do" failure mode.

### What is the second biggest risk, and how do we brand around it?

Stripe Connect onboarding friction is the second biggest risk. A non-trivial share of developers, especially outside the US, bounce during identity verification. We are partnering with Stripe on a smoother in-product flow and on a "sandbox to live" path that lets the developer keep building before verification clears. We are tracking activation-to-first-payment as the key funnel metric, not activation-to-deploy. On branding, Launchpad is a product, not a separate brand. It is "ShipNow Launchpad" everywhere, never just "Launchpad." The marketing surface lives inside the main site, not a separate microsite. The goal is for a new developer to perceive Launchpad as the easy on-ramp to ShipNow, not as a different company. This also keeps us from confusing the procurement conversation later, when an enterprise buyer is evaluating ShipNow as a vendor and discovers their internal teams are already using Launchpad.

### What is explicitly out of scope for the launch?

Multi-region active-active. Self-hosted Launchpad. Mobile app builds. Native desktop builds. On-prem deployment. FedRAMP. HIPAA workloads. Agency multi-tenant billing. A marketplace of third-party Launchpad templates.

Each of these has been requested. None of them are in the launch scope. Several are on the roadmap. We will publish a public roadmap at launch with a clear note that roadmap items are not commitments.

### What does success look like 90 days post-launch?

Three numbers. First, number of Launchpad projects that have taken at least one real payment from a non-founder customer. Second, percentage of those projects still active and billing 60 days after first payment. Third, share of new ShipNow signups that come in through the Launchpad front door rather than the standard PaaS signup. The first number is the leading indicator of category fit. The second is the leading indicator of platform quality. The third is the leading indicator of the trojan-horse motion working.

### What would cause us to pull the launch, and what is our messaging discipline around AI?

Three things would cause a pull.

A sustained first-deploy success rate under 75 percent on a representative repo sample. An unresolved data-isolation defect in the multi-tenant managed services. A finding from the pre-launch security review that requires architectural rework.

None of these are currently flagged red. The first is flagged yellow and is the active focus of the platform team.

On AI messaging discipline: Launchpad is not marketed as an "AI builder" or a "vibe coding platform," even though the interview step uses an LLM and the framework detection uses one as well. The promise we are selling is "your repo becomes a business," not "describe an app and we generate one." We do not want to be confused with no-code app generators, and we do not want to attract users whose repos do not actually exist yet. Marketing, sales, and docs all hold this line. If a press question pushes on the AI angle, the approved answer is that AI is plumbing, not the product.
