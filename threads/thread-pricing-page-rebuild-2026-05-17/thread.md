# #proj-pricing-rebuild

**priya.k (PM)** 09:42
ok so kicking this off. exec readout was rough. takeaway: current pricing page is "a wall of plans nobody understands." we have 6 SKUs on the public page right now, conversion on the page itself is like 1.4%. @channel we need a plan by EOW.
:eyes: 4, :fire: 2

**priya.k** 09:43
goal from leadership: consolidate to 3 tiers, add annual/monthly toggle, and there's appetite for a usage calculator for the enterprise tier.

**mark.d (Design)** 09:44
finally lol. i've been saying the 6-plan grid is unreadable for like a year

**mark.d** 09:44
the comparison table alone has 47 rows

**priya.k** 09:45
yeah. fwiw legal hasn't reviewed that table in ~18 months so we should probably loop them in regardless of what we ship
:thinking_face: 2

**raj.s (Eng)** 09:47
quick q before we go too far: are we keeping the "free forever" tier? because if not the migration story for the ~12k accounts on it is non trivial

**priya.k** 09:48
tbd honestly. exec is split. some want to kill it some want to keep as acquisition funnel

**mark.d** 09:49
killing free would tank top of funnel. i'd push back hard on that

**raj.s** 09:49
+1

**priya.k** 09:50
ok let's table free-tier-kill for now. assume it stays on the page unless someone screams. moving on.

**lena.r (Marketing)** 09:52
catching up. couple things from my side:
- annual toggle is table stakes at this point, competitors all have it
- the calculator needs to feel like a "tool" not a form. like notion's or vercel's, not a quote request
- ALSO we need to handle EU VAT display properly, finance flagged this last quarter and it never got done
:100: 1

**raj.s** 09:53
oh god the VAT thing

**raj.s** 09:54
that's actually a bigger lift than the calculator imo. detecting region, showing inclusive vs exclusive, and we don't have a currency switcher at all right now. everything is USD.

**lena.r** 09:54
right. and i18n more broadly. are we shipping this english-only or are we doing the 4 locales?

**priya.k** 09:55
english only for v1. localization is its own epic.

**lena.r** 09:55
ok but currency switcher is separate from translation right

**raj.s** 09:56
yes but they're related. if i show €99 i need to know what locale conventions to use for the number formatting. "1.299,00 €" vs "€1,299.00". that's intl.NumberFormat stuff, not translation, but it still needs the locale.

**priya.k** 09:57
ok noted. currency switcher in scope, full translation out of scope. raj can you spike on what "currency only, locale-aware formatting" looks like?

**raj.s** 09:57
yeah will do. give me a couple days

---

**mark.d** 10:14
question for the room: do we show the enterprise/contact-sales tier on the public page at all? or do we hide it behind a "talk to sales" CTA?
:eyes: 3

**lena.r** 10:15
show it. hiding it is a self-own. people google "[us] pricing enterprise" all day

**mark.d** 10:15
counter: showing a tier with no price makes the other tiers look more expensive by anchoring

**lena.r** 10:16
that's... actually the point? lol

**mark.d** 10:16
fair

**priya.k** 10:17
show it. but make the calculator the hero of that tier so it doesn't feel like a dead end. "estimate your cost" CTA inline.
:rocket: 2, :100: 1

**mark.d** 10:18
ok works. that's the slider/input combo we sketched in figma last week right

**priya.k** 10:18
yes that one

**mark.d** 10:19
ngl i'm worried about the slider a11y wise. screen readers don't love range inputs with non-numeric labels and we have stuff like "team size" "events per month" "data retention" that all want different controls

**mark.d** 10:20
also slider thumbs are a known pain point for motor accessibility. we should at least have a paired number input for each one

**raj.s** 10:20
+1 paired inputs. that also makes the test story way easier

**priya.k** 10:21
ok. slider + paired number input, both bound. mark can you spec the a11y requirements explicitly? we got dinged on AA last audit

**mark.d** 10:21
yep on it

---

**lena.r** 10:33
side thought: the comparison table. are we redoing it from scratch or porting the current rows?

**mark.d** 10:34
god please not porting. half those rows are features nobody uses or have been renamed

**priya.k** 10:35
agreed redo. but that means PMM has to re-bless every feature row. lena can you own the row list?

**lena.r** 10:35
ugh. yes. fine.
:sob: 1, :pray: 2

**lena.r** 10:36
but legal HAS to review before we ship. we say "unlimited" in like 4 places and it's not actually unlimited

**priya.k** 10:36
yes. legal review is a hard gate on the comparison table. blocking.

---

**raj.s** 11:02
DM-ish but posting here for the record: i talked to @priya.k offline about the timeline. we're not committing to a ship date in this thread, there's too much unknown (VAT scope, legal turnaround on the table, free tier decision pending). we'll scope after the spike.

**priya.k** 11:03
confirming. no ship date today. we'll set one once raj's spike lands and legal gives us a turnaround estimate.

**lena.r** 11:04
ok. but informally i'd love this live before our Q3 campaign kicks off. just flagging.

**priya.k** 11:04
noted, not committed.

---

**mark.d** 11:30
ok summarizing what i think we agreed on, yell if wrong:
- 3 tiers public (consolidating from 6)
- free forever stays (default assumption, not killed)
- enterprise tier visible on public page, calculator is the hero
- annual/monthly toggle
- usage calculator: slider + paired number input, AA accessible
- currency switcher with locale-aware formatting, english copy only
- comparison table rebuilt from scratch, legal review required before ship
- ship date: TBD, gated on raj spike + legal estimate

**priya.k** 11:31
that's it. nice.
:100: 3, :rocket: 2

**raj.s** 11:32
one more: who owns the migration comms for accounts on the 3 SKUs we're killing? that's ~8k paying accounts.

**priya.k** 11:33
oh. good catch. that's a lifecycle/CX thing. i'll talk to support lead. parking it as a dependency, not in scope for the page itself but blocks launch.

**lena.r** 11:33
yeah it absolutely blocks launch, you can't redirect a SKU someone's subscribed to without notice

**priya.k** 11:34
agreed. adding to the risk list.

**mark.d** 11:35
oh and (edited) one last thing — the page needs to handle the "grandfather" case visually. some legacy plans aren't going anywhere, they just won't be sold. need a state for "you're on a plan we don't sell anymore, here's what's equivalent."

**priya.k** 11:36
ugh. yes. ok that's a logged-in state on the pricing page basically. let's call that out as a separate flow.

**raj.s** 11:37
that's a whole thing. logged-in pricing page is different infra from the marketing page in our stack right now

**priya.k** 11:38
i know. parking. let's get the spike done first and decide scope after.

**lena.r** 11:38
ok i need to drop. good thread. priya can you doc this up?

**priya.k** 11:39
yes will write it up today. thanks all.
:heart: 2, :pray: 1
