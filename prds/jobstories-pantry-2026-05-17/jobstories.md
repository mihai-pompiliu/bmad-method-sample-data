# Pantry: Job Stories

**Product:** Pantry (working name)
**Author:** PM
**Date:** 2026-05-17
**Format:** Jobs-To-Be-Done, job-stories format
**Status:** Pre-spec, intended as input to a Spec kernel

## Product context

Pantry is a kitchen-pantry tracking app for households who actually cook at home. It tracks what you have, when it expires, and what you can make with what is on hand. It is shared across a household so a partner, a roommate, or a teenager can keep the inventory honest without one person policing it.

Pantry is deliberately not a meal planner, not a recipe app, and not a grocery delivery service. Those categories are well served. The unmet job is the boring middle: knowing the state of your kitchen so the other apps (and your own brain) can do their work.

## The job we are hired to do

When I am running a household that cooks, I want to know what food I have, what is about to go bad, and what I can do with it, so I can waste less, spend less, and stop staring into the fridge at 6:47pm wondering what is for dinner.

## Job stories

### Situation: bringing groceries home

When I am putting groceries away after a Costco run, I want to log new pantry items quickly (ideally by scanning), so I can capture what I have without making it a chore.

When I just unpacked a delivery order, I want the items I bought to land in my pantry automatically, so I do not have to double-enter what I already told the store.

When I buy something in bulk (six cans of tomatoes), I want to track quantity, not just presence, so I know when I am actually getting low.

### Situation: at the store

When I am at the grocery store and standing in front of olive oil, I want to check what I already have at home, so I can avoid buying duplicates and over-buying.

When I am writing a shopping list, I want to see what I am running low on without typing it out, so the list reflects reality and not just what I happened to remember.

When my partner is at the store and I am at home, I want them to see the same live list, so we do not come home with two jars of peanut butter again.

### Situation: about to waste food

When I am about to throw out the third tomato I forgot about this month, I want a gentle nudge earlier about what is about to expire, so I can use ingredients before they spoil.

When something has been sitting unused for a long time, I want a quiet flag (not a guilt trip), so I can decide to use it, freeze it, or accept the loss.

When I open the fridge and see a sad bag of greens, I want a one-tap suggestion for "use this tonight", so the decision is made for me.

### Situation: deciding what to cook

When I am deciding what to cook tonight and I am tired, I want to see what I can make with what is on hand, so I can avoid another takeout decision.

When I have 20 minutes and three ingredients I need to use, I want low-effort dinner ideas filtered by what I actually have, so the constraint becomes the answer instead of the problem.

When I am cooking for kids who are picky, I want to filter suggestions by what they will actually eat, so I am not negotiating at the table.

### Situation: dietary needs

When someone in the household has a dietary restriction (gluten-free, nut allergy, vegetarian), I want suggestions and inventory views to respect that, so I do not have to mentally filter every time.

When I am hosting someone with a restriction, I want to quickly see what in my pantry works for them, so I can plan without re-reading every label.

### Situation: keeping the inventory honest

When a family member finishes the last of something, I want them to mark it without thinking about it, so the inventory stays accurate without me policing it.

When the inventory is wrong (we used the rice but did not log it), I want to fix it in two taps, so the cost of being honest is lower than the cost of ignoring the app.

When I am doing a weekly inventory pass, I want to scan a shelf quickly with my camera, so I can update what is there without typing each item.

### Situation: noticing patterns

When I notice we always run out of olive oil (or coffee, or eggs), I want the app to surface that pattern, so I can buy enough next time instead of running out again.

When I am batch cooking on a Sunday, I want to see what I have in larger quantities, so I can plan a batch around what is already abundant.

When kid snacks are running low and the week is not over, I want an early signal, so I can restock before the meltdown.

### Situation: sharing the load

When I share the household with a partner, I want both of us to see and edit the same pantry, so it is not "her app" or "his app".

When a teenager grabs the last granola bar, I want them to be able to mark it gone in one tap (or via a sticker on the shelf), so the system survives contact with real humans.

## Forces (JTBD four-forces)

### Push: what makes me hire this product

Food waste guilt. The recurring small shame of throwing out produce I meant to use.
Decision fatigue. The 6pm "what is for dinner" question, every weeknight, forever.
Takeout cost creeping up because cooking feels harder than it should.
Buying duplicates because I cannot remember what is in the back of the pantry.
A vague sense that I am spending more on groceries than I should and cannot see why.

### Pull: what attracts me to this product

A friend showing me their pantry app and it actually works for their family.
A clean interface that does not feel like enterprise inventory software.
The family-sharing model (this is the unlock for households where one person is currently doing all the cognitive work).
A scanner that is fast enough to actually use while putting groceries away.
The promise of less waste, framed in dollars and not just sustainability.

### Anxieties: what makes me hesitate

Yet another app. I already have too many.
Privacy. I do not want my grocery data sold or used to target me.
"Will my partner actually use it?" If it becomes a one-person project, it fails.
Setup cost. If I have to enter 200 items to get started, I will not.
Accuracy decay. I have tried this before with a list app and it drifted into uselessness within two weeks.

### Habits: what I am doing now instead

A paper list on the fridge.
A running note in the Notes app, shared with a partner.
Just my memory, which is increasingly unreliable.
A shared text thread where we tell each other what to buy.
Walking to the pantry, opening the door, and staring.
The grocery store app's "buy again" list as a de-facto inventory proxy.

## Out-of-job (we are not hired to do this)

We are not hired to plan meals weekly. Meal planning is a different job, done by different people, at a different cadence. We will be friendly to meal planners but we will not become one.

We are not hired to deliver groceries. We will not build a storefront, a delivery network, or a partnership-first model where the app exists to sell things.

We are not hired to track macros, calories, or nutrition goals. Fitness and nutrition tracking is a different category with different users and different anxieties.

We are not hired to manage restaurant kitchens, commercial inventory, or food-service compliance. Different scale, different shape of the problem.

We are not hired to be a recipe app. We will surface "what you can make" but the recipe content itself is not the product.

## Success indicators (job-completion-shaped)

A user reports their weekly food waste has dropped measurably within 60 days of starting (self-reported survey, with a baseline taken at signup).

A household maintains accurate inventory for at least 70% of pantry items in any given week (sampled audits with consenting households).

A user reports lower decision-fatigue around weeknight cooking, specifically the "what is for dinner" question (qualitative interview signal, repeated at 30 and 90 days).

The second adult in a household actively uses the app at least weekly (multi-user activation, not just installs).

Takeout frequency self-reported as lower 90 days in (loose proxy, but directionally honest).

The "I almost forgot about this" save: a user can recall at least one instance per month where the app surfaced something they would have wasted.

## Open questions

Is the scanner fast enough to be a hire-able experience, or is barcode-only the right v1?
Does receipt parsing (from grocery emails or photos) move the needle on setup cost?
Where is the right line between "useful nudge" and "annoying notification" on expiry warnings?
Does the family-sharing model need a "low-friction guest" tier for teenagers and roommates who will not create an account?
Should "what can I make" be a first-class surface or a side feature, given we explicitly refuse to be a recipe app?
What is the right primitive for quantity? Count, weight, or a fuzzy "enough / low / out"?
