# Feature request: first-class union schema (`oneOf` / `anyOf`) without writing a custom validator

**Labels:** `enhancement`, `schema`, `type-inference`, `discussion`

---

### @lena-hofstra · 2026-05-12

Hey folks, long-time user, first-time issue filer. Loving `valido` so far, it replaced about 400 lines of hand-rolled validation in our checkout service.

The one thing I keep tripping over is union types. Specifically, when an API field can be either a string ID or a fully expanded object. Right now I end up writing a custom validator with `v.custom(...)` every time, and I lose all the nice inference downstream.

What I want to write:

```ts
const PaymentMethod = v.union([
  v.string(),
  v.object({
    id: v.string(),
    last4: v.string(),
    brand: v.enum(["visa", "mc", "amex"]),
  }),
]);

type PaymentMethod = v.Infer<typeof PaymentMethod>;
// expected: string | { id: string; last4: string; brand: "visa" | "mc" | "amex" }
```

The closest thing I found in the docs is `v.either(a, b)` but that only takes two schemas, and the inferred type comes back as `unknown` (see `src/schemas/either.ts` line 47-ish, the `Infer` branch falls through to the default case).

Is there a path to a real `union` / `oneOf` here? Happy to take a stab at a PR if there's appetite.

---

### @rmarchetti-dev · 2026-05-12

+1, ran into the exact same thing yesterday with a webhook payload that has a polymorphic `event.data` field. Ended up doing:

```ts
const Event = v.object({
  type: v.string(),
  data: v.custom((val) => {
    // 30 lines of branching here, yikes
  }),
});
```

and the inferred type for `data` is just `unknown`, which defeats most of the reason I picked this library over the alternatives.

---

### @priya-codes · 2026-05-13

> Is there a path to a real union / oneOf here?

There's some prior discussion in #284 and an abandoned PR (#312) that tried to add this. Worth reading through before you start a new one, @lena-hofstra. From what I remember the sticking point was discriminated unions vs. untagged unions and what the runtime check actually does when both branches partially match.

---

### @tobias-vk · 2026-05-13 (maintainer)

Thanks for the writeup @lena-hofstra. To set expectations up front: the lack of a general `v.union` is by design, not an oversight.

The core issue is that `valido`'s inference engine relies on every schema node having a single, statically known output type. Once you introduce `A | B` at the schema level, two things break:

1. Error reporting. If validation fails, we currently return a path + a single expected type. With unions we have to return a tree of attempted branches, which is a much bigger API surface and a much worse default error message for the 90% case.
2. Refinement. `v.refine(schema, predicate)` assumes the predicate narrows a known type. With a union input, the predicate has to be written against `A | B`, which means users end up writing the branching logic in the predicate anyway. We haven't found a clean way around that.

`v.either(a, b)` exists specifically because two-branch unions are tractable, the error message can name both branches, and the inferred type stays manageable. The fact that `Infer` falls through to `unknown` for it is a bug, not a deliberate signal, and we should fix that regardless of what we decide here.

I'm not saying never, but I want to be honest that I'm skeptical.

---

### @lena-hofstra · 2026-05-13

Appreciate the candor @tobias-vk. A few pushback points:

On (1), error reporting: I think the "tree of attempted branches" output is actually what most users want when a union fails. Right now with `v.custom` we get a single useless `"custom validation failed"` string, which is strictly worse than even a verbose union error. I'd rather have noisy-but-useful than terse-but-opaque.

On (2), refinement: agreed that this is genuinely thorny, but I'd argue you can document it as "refinements on unions receive the union type, narrow it yourself." That's how every other library in this space handles it.

Repro for the `v.either` inference bug, since you mentioned it:

```ts
import { v } from "valido";

const S = v.either(v.string(), v.number());
type T = v.Infer<typeof S>;
//   ^? unknown   <-- expected: string | number

const result = S.parse(42);
// result.value is typed `unknown`, even though runtime check passed
```

Reproduces on `0.18.3` and on `main` as of `a4f2c1d`. Happy to file this as a separate issue if you'd prefer to keep this thread focused on the union design question.

---

### @priya-codes · 2026-05-13

That inference fallthrough has been there since at least `0.14`. I remember hitting it and just assuming it was intentional because the docs for `either` are vague on the type story.

---

### @tobias-vk · 2026-05-13 (maintainer)

@lena-hofstra please do file the `Infer<either>` bug separately, I'll tag it as a quick fix. The fix is one line in `src/infer/either.ts`, we just never wired the union type through to the `Infer` mapped type.

On the broader feature: I hear you on "noisy beats opaque." Let me think about it.

---

### @grumpy-ops-guy · 2026-05-14

Drive-by from someone who maintains a wrapper around valido at $dayjob. We use it for validating Kafka message envelopes and we 100% need unions. We solved it with a discriminator pattern:

```ts
const Message = v.discriminated("type", [
  v.object({ type: v.literal("user.created"), payload: UserCreated }),
  v.object({ type: v.literal("user.deleted"), payload: UserDeleted }),
  v.object({ type: v.literal("order.placed"), payload: OrderPlaced }),
]);
```

This is essentially TypeScript's discriminated union, and it sidesteps most of @tobias-vk's concerns because the discriminator field tells you exactly which branch to validate against. No ambiguity, single error path, clean inference.

I'd much rather see `v.discriminated` land as a first-class feature than a general `v.union`. The general case is a footgun, the discriminated case is just good schema design.

---

### @rmarchetti-dev · 2026-05-14

`v.discriminated` would cover my webhook use case completely. +1 from me.

---

### @lena-hofstra · 2026-05-14

I'd take `v.discriminated` happily, but it doesn't cover my original case (string-or-object for a polymorphic ID field, no discriminator possible because one branch is a primitive). I think both are valuable but they're solving different problems.

---

### @priya-codes · 2026-05-14

For the primitive-or-object case, isn't that what `v.either` is already for? The only blocker is the inference bug. If `Infer<v.either<A, B>>` correctly resolved to `Infer<A> | Infer<B>`, would that close out @lena-hofstra's original ask?

---

### @lena-hofstra · 2026-05-14

Mostly yes, with one caveat: `v.either` is binary. If I have three branches (string, number, object) I'd have to nest `v.either(v.either(a, b), c)` which is ugly and probably breaks error messages further. An n-ary version, even if it's still called `either`, would be enough.

---

### @kfischer-contrib · 2026-05-15

Tangent, sorry: this conversation is bringing back PR #312 PTSD. For anyone who didn't follow it, that PR tried to land `v.union` as a variadic function but the implementation walked every branch on every validation call, which made deeply-nested schemas O(n^m) in the worst case. The benchmark suite tanked and it got closed.

If we revive this we have to commit upfront to a "try-in-order, stop on first success, no backtracking" semantics, otherwise we'll end up in the same swamp. Also, the discriminated version sidesteps this entirely because it's O(1) branch selection.

---

### @tobias-vk · 2026-05-15 (maintainer)

Reading the thread, I think there's a viable path that addresses most of what people are asking for:

1. Fix `Infer<v.either<A, B>>` to correctly resolve to `Infer<A> | Infer<B>`. This is happening regardless, will tag as a separate issue.
2. Add `v.discriminated(key, schemas[])` as a new first-class schema. Clean inference, clean errors, no backtracking, no ambiguity. This is the 80% case for real-world APIs.
3. Hold off on a general n-ary `v.union` / `v.oneOf` until we have a clearer answer on error reporting and the refinement semantics.

I think (1) and (2) together cover roughly 85-90% of legitimate union use cases. (3) is the one I'm still nervous about.

---

### @grumpy-ops-guy · 2026-05-15

This is a sensible plan and I'd support it. Happy to help review a `v.discriminated` PR.

---

### @nyx-builds-things · 2026-05-15

Could we also add `v.intersection` while we're in this area of the codebase? I keep needing to merge two object schemas and there's no clean way to do it without `v.object({ ...a.shape, ...b.shape })` which loses refinements.

Also a `v.partial(schema)` helper that makes all object fields optional would be huge for PATCH endpoints.

---

### @tobias-vk · 2026-05-15 (maintainer)

@nyx-builds-things both reasonable asks but please open separate issues, let's keep this thread scoped to union types or it's going to balloon and nothing will get decided. I'll watch for the issues and triage them.

---

### @lena-hofstra · 2026-05-16

@tobias-vk the proposed plan works for me. To make sure I'm reading you correctly: are you saying a PR for `v.discriminated` would be welcome now, or do you want a design doc first?

For the n-ary `v.union` question, would it help if I wrote up a short design spec covering:

- Error reporting strategy (tree-of-attempts vs. flat)
- Refinement semantics on union inputs
- Try-in-order vs. validate-all-and-pick semantics
- Inference rules for nested unions

If we had that written down, even if we don't act on it now, future-us has something to point at when this comes up again (which it will).

---

### @tobias-vk · 2026-05-16 (maintainer)

Design doc would be very welcome, yes. For `v.discriminated`, I'd like to see a short proposal in an issue or RFC before a PR, mostly so we agree on the API shape (positional vs. object args, how literal discriminators interact with `v.enum`, etc). I don't want to send you down a 500-line PR rabbit hole and then bikeshed the function signature.

Adding `needs-design` to this issue and leaving it open. Let's reconvene once @lena-hofstra has a draft, or if someone else picks up the design work.

---

### @priya-codes · 2026-05-16

Subscribing. Worth noting for anyone landing here from search: the immediate workaround for the binary-union case is still `v.either` plus a manual type assertion until the `Infer` fix ships. Not pretty but it works.

---

### @rmarchetti-dev · 2026-05-17

Same, watching this one. Thanks @tobias-vk for engaging seriously with the pushback instead of just closing it.

---

*Issue remains open. Labels updated: `enhancement`, `schema`, `type-inference`, `discussion`, `needs-design`.*
