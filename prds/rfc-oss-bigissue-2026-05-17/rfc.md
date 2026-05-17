# [RFC] Saved Queries: named, shareable issue searches

**Author:** @priya-k
**Labels:** `rfc`, `triaged`, `area/search`, `breaking-change`, `needs-design-review`
**Milestone:** v2.1
**Status:** Open for comment until 2026-06-01

---

## Motivation

BigIssue's query DSL is powerful but expensive to type. Power users (triage leads, SREs, PMs) end up pasting the same 80-character query strings into Slack, into onboarding docs, into the URL bar. Three concrete pains we hear in user research:

1. "I have a triage query I run 12 times a day. I have it in a text file."
2. "I shared a URL with my teammate. The URL works, but they can't remember it tomorrow."
3. "My team has a 'P0 customer-reported regressions' view. Onboarding new triagers means teaching them the magic string."

We propose **Saved Queries**: a first-class object that wraps a query expression, gives it a name, and lets it be shared across a workspace or kept private to a user.

## Proposal

Add a new object type `SavedQuery` with the following shape:

```yaml
saved_query:
  id: sq_01HXYZ...           # ULID
  name: "P0 customer regressions"
  slug: "p0-customer-regressions"
  query: "label:p0 label:regression source:customer is:open"
  scope: workspace            # one of: user | workspace
  owner: user_abc123
  workspace: ws_xyz789
  created_at: 2026-05-17T...
  updated_at: 2026-05-17T...
  description: "Used by triage rotation. See runbook." # optional
```

Users can:

- Create one from the current query: `bi query save "P0 customer regressions"`
- Run one by name: `bi query run "p0-customer-regressions"` or visit `/q/p0-customer-regressions`
- List them: `bi query list [--scope workspace|user|all]`
- Edit, rename, delete via CLI or UI
- Star them (per-user, surfaces in the sidebar)

## Detailed Design

### §1. Storage

Saved queries live in the main SQLite database, in a new table `saved_queries`. Indexes on `(workspace_id, slug)` (unique) and `(owner_id, scope)`.

We considered two alternatives:

| Option | Pros | Cons |
|---|---|---|
| **SQLite (proposed)** | Transactional with issues. Queryable. Migrations are routine. | Couples saved-query storage to the main DB. |
| Workspace settings file (`bigissue.toml`) | Git-trackable. Reviewable in PRs. | No per-user scope. Edits race. No ULIDs. |
| Pure URL encoding (stateless) | Zero storage. Easy share. | No name. No discoverability. No edit. |

URL-encoded shares remain supported (they already work today) and are orthogonal to this feature.

### §2. Scope and permissions

Two scopes at launch:

- `user`: visible only to the owner. Stored with `workspace_id` for context but not surfaced to others.
- `workspace`: visible to all members of the workspace. Editable by the owner and workspace admins.

We deliberately defer team-scoped queries (`scope: team`) to a follow-up RFC once the team object lands in v2.2.

### §3. Resolution

When a query is run, the saved query's `query` field is parsed by the existing query parser. **No new DSL syntax is introduced.** A saved query is a name on top of a string the parser already understands.

This means saved queries automatically pick up parser improvements. It also means they break when we make breaking DSL changes (rare, but see Drawbacks).

### §3.2. (EDIT, added after [#7 below](#comment-7)) Parameterized queries

Saved queries may include positional placeholders `$1`, `$2`, etc.:

```yaml
query: "assignee:$1 is:open label:p0"
```

Invoked as `bi query run customer-on-call @alice`. Parameters are typed as `user | label | string | int` and validated at run time. We considered named parameters but positional keeps the v1 surface small.

### §4. URLs

Saved queries get a stable URL per workspace: `/{workspace}/q/{slug}`. Slug is derived from name, kebab-cased, deduped with a numeric suffix. URLs are guessable by design (saved queries within a workspace are not secret from workspace members).

### §5. CLI surface

```
bi query save <name> [--scope user|workspace] [--from <existing-query>]
bi query run <name-or-slug> [params...]
bi query list [--scope ...] [--mine] [--starred]
bi query show <name-or-slug>
bi query edit <name-or-slug>
bi query rm <name-or-slug>
bi query star <name-or-slug>
```

## Drawbacks

1. **New permission model.** Even with only two scopes, "who can edit" is a new concept (owner-or-admin). We have not had to think about cross-user mutation before.
2. **Query bloat.** Workspaces will accumulate stale saved queries. No proposed garbage-collection mechanism in v1. We rely on the list UI surfacing "not run in 90 days."
3. **DSL drift.** If we change the DSL in v3, saved queries written against v2 may silently parse differently. Mitigation: store the parser version on the row, warn on mismatch.
4. **Sharing leaks.** A workspace-scoped saved query may surface issues to a member who couldn't have constructed the query themselves. This is by design (workspace members already see all workspace issues today) but becomes more surprising if we later add per-issue ACLs.

## Alternatives

- **Browser bookmarks.** The status quo. Doesn't share, doesn't name, doesn't survive URL changes.
- **Aliases in shell config.** Doesn't help web users. Doesn't share across machines.
- **Full "Views" object** (à la some competitors) with custom columns, sorting, grouping. We see this as a separate feature, layered on top of saved queries.

## Unresolved Questions

- Should saved queries appear in `@`-mention autocomplete inside issue comments? ("See @triage-board")
- Do we need an audit log for workspace-scoped edits?
- How do saved queries interact with the export feature?
- Should the URL slug be globally unique or workspace-scoped? (Currently proposing workspace-scoped.)

---

## Comments

---

**@dmitri-w** commented:

Strong +1 on the motivation section. I literally have a `triage.txt` on my desktop. One question: §3 says "no new DSL syntax." Does that hold once you add parameters in §3.2? `$1` is new syntax, no?

👍 4

---

**@priya-k** (author) commented:

Fair catch. §3.2 was added after the original post (see EDIT marker). I'll soften the §3 language to "no new query operators, only a parameter-substitution layer on top." The parser still sees a fully-substituted string.

👍 6

---

**@rin-okafor** commented:

I want to push back on workspace-as-default-scope. Most of my saved queries are personal scratch. If I have to remember to pass `--scope user` every time, I'll forget once and pollute the workspace list. Suggest defaulting to `user` and requiring an explicit `--scope workspace` (or a `--share` flag).

👎 1 👍 12

---

**@priya-k** (author) commented:

@rin-okafor I went back and forth on this. The user-research signal was that people want to share, and a default of `user` means everyone forgets to share the genuinely-useful ones. But I take your point that the failure mode of accidental-share is worse than accidental-private. Persuaded. Will flip the default to `user` and add a prominent "share" affordance.

EDIT: Updated §2 to reflect this. Default scope is now `user`.

👍 18 🚀 3

---

**@rin-okafor** commented:

Thanks for the quick turn. Convinced.

---

**@jflores-sec** commented:

Security concern that I don't see addressed. Consider a workspace with mixed-tier members (e.g., contractors with read access to a subset of projects). If an admin saves a workspace-scoped query that returns issues from a restricted project, and a contractor runs it, what happens?

Either:
(a) the query runs as the saver's identity and returns issues the contractor shouldn't see (PII leak), or
(b) the query runs as the caller's identity and returns a different result set than the saver intended (confusing, may leak query intent).

We need to pick (b) and be explicit. (a) is a vulnerability.

👍 22 🚀 8

---

<a id="comment-7"></a>**@priya-k** (author) commented:

@jflores-sec This is exactly the kind of case I didn't think hard enough about. You're right that (b) is the only safe answer. Let me add a §6 "Authorization" section spelling this out, plus a non-goal: a saved query is a saved *string*, not a saved *result set*. Results are always computed against the caller's view of the world.

Adding to Unresolved Questions: should the UI warn when a saved query returns 0 results for the current user but >0 for the owner? That could itself be a side channel.

👍 14

---

**@maintainer-bot** commented:

Auto-labeled: `security-review-required`

---

**@kev-rs** commented:

Engineering critique on §1 (storage). I've stared at the `saved_queries` table proposal and I think the invariants are weaker than they look.

You have `(workspace_id, slug)` unique, fine. But `owner_id` is also a foreign key, and `scope = user` queries are conceptually owned by `(workspace_id, owner_id)` together. What happens when a user is removed from a workspace?

Three sub-cases:
1. Soft-remove (user still exists, membership revoked): their `user`-scoped queries become orphaned but still exist in the table. UI should hide them. Migration: trivial.
2. Hard-delete (user record gone): FK cascade deletes their queries. Fine, but workspace-scoped queries owned by that user also vanish, which is bad. A workspace-scoped query should outlive its creator.
3. Re-add: the slugs might collide with newly-created ones.

Concrete suggestion: split ownership. `created_by` is informational (nullable on user delete). For workspace-scoped queries, ownership transfers to the workspace on creator-removal. For user-scoped, cascade-delete is fine. This is a real schema decision, not a footnote.

👍 27 🚀 11

---

**@priya-k** (author) commented:

@kev-rs This is the comment I was hoping someone would write and dreading at the same time. Agreed across the board. Will rework §1 to:

- `created_by` (nullable, informational)
- `owner_type` enum: `user | workspace`
- `owner_id` (polymorphic, references whichever)
- On user removal, workspace-scoped rows reassign `owner_type=workspace`; user-scoped rows cascade-delete.

This also cleans up the permission model (§2). "Who can edit" becomes "the owner, by type."

Pushing an update tonight.

👍 19

---

**@tomas-ng** commented:

nit: typo in §4, "guessable by design" reads like a security claim. Suggest "predictable by design" or "stable across renames."

👍 3

---

**@priya-k** (author) commented:

@tomas-ng Fixed, thanks.

---

**@dmitri-w** commented:

Coming back to this. What about the export feature? If I `bi export --query "label:p0"` today, can I `bi export --saved-query p0-customer-regressions` tomorrow? Feels like a natural pairing but I don't see it called out.

👍 9

---

**@priya-k** (author) commented:

@dmitri-w Good point, this was an unresolved question and I want to commit to an answer: yes. Export accepts either a raw query or a saved-query reference, with the same authorization rule (runs as caller). I'll add this to §5 (CLI surface) and remove the export question from Unresolved.

---

**@samir-h-maintainer** (maintainer) commented:

Reviewed end-to-end. Rough consensus is forming. My take:

**LGTM for v2.1 minus the parameterized-query bit (§3.2).** Parameters introduce a typing system and validation surface that I don't want to ship in the same release as the core feature. Strong preference: split §3.2 into a follow-up RFC, ship the rest in v2.1.

**Blocked on v2.1 ship until:**
1. §1 schema reworked per @kev-rs's comment. Non-negotiable, the invariants matter.
2. §6 "Authorization" section added per @jflores-sec. Caller-identity resolution must be explicit.
3. Migration plan for existing URL-shared queries (do we offer to convert them?).

**Next steps:**
- @priya-k to revise the RFC by 2026-05-24
- @jflores-sec to do a security review pass on the revision
- @kev-rs to sanity-check the schema diff
- Re-review at the v2.1 planning meeting on 2026-05-28
- If we land v2.1 saved queries, file the parameterized-query RFC against v2.2

Thanks @priya-k for a well-structured proposal and for being responsive to feedback. This is what the process should look like.

👍 41 🚀 18 ❤️ 9

---

**@priya-k** (author) commented:

Thanks @samir-h-maintainer. Splitting §3.2 out works for me. Will have the revision up by Friday.
