---
layout: default
title: Comments & Documentation
parent: Code I Prefer
nav_order: 7
---

# Comments & Documentation
{: .no_toc }

The default is **no comment**. Good naming, small functions, and clear types do most of the work documentation pretends to do. I write a comment only when the **why** is non-obvious — and I'll delete a stale comment faster than I'll delete dead code.

---

## The why-not-what rule

```ts
// Bad — restates the code in English
// Increment counter by 1
counter += 1;

// Bad — describes what the function does (the name already does that)
// Fetches the user by ID and returns it
async function getUser(id: string) { ... }

// Good — explains a non-obvious constraint
// Stripe webhook IDs are not idempotent across retries when the event
// is delivered via the CLI proxy, so we dedupe on (event.id, livemode).
const key = `${event.id}:${event.livemode}`;
```

Comments belong where reading the code alone would leave a competent developer asking "wait, why?"

- A workaround for a specific bug (link the issue)
- A subtle invariant the type system can't express
- A performance choice that looks weird
- A historical reason the obvious approach doesn't work

---

## Comments that should be deleted on sight

- `// TODO: refactor this` with no date and no owner — it'll still be there in three years
- `// added by Ahmad for the chat flow` — git blame already knows
- `// removed login button (see PR #142)` — the PR is the record, not the comment
- Multi-paragraph docstrings on internal functions — JSDoc on a 4-line helper is theatre

If a `TODO` is real, it has a tracker link: `// TODO(LIN-482): replace with real pagination once the API supports cursors`.

---

## JSDoc only on public boundaries

I don't write JSDoc for every function. I write it for **exported** functions that other features (or external consumers) will call — and only when the signature alone isn't enough:

```ts
/**
 * Refreshes the access token using the stored refresh token.
 * Throws ApiError(401) if the refresh token is also invalid —
 * callers should treat this as "force logout".
 */
export async function refreshAccessToken(): Promise<string> { ... }
```

The throwing behavior isn't visible in the type, so it earns a doc. The function name and return type already say "what" — the doc says "what could go wrong."

---

## Don't document the obvious; do document the surprising

```ts
// Bad — the type says everything already
interface User {
  /** The user's ID */
  id: string;
  /** The user's name */
  name: string;
}

// Good — captures a real constraint
interface User {
  id: string;
  /**
   * Display name. May be empty for OAuth-imported users until they
   * complete profile setup. Don't render directly — use `formatUserName(user)`
   * which falls back to email-local-part.
   */
  name: string;
}
```

---

## README and design docs

A `README.md` at the repo root and one in each non-trivial feature folder. The README covers:

1. **What is this?** (one paragraph)
2. **How do I run it?** (commands)
3. **How is it organized?** (link to architecture notes)
4. **How do I add a new feature?** (the path of least surprise)

For larger architectural decisions, I write an ADR (Architecture Decision Record) — short markdown file with context, decision, alternatives considered, and consequences. ADRs are append-only; if I change my mind, I write a new one that supersedes the old one. The history matters.

---

## Comments rot. Code is the source of truth.

A wrong comment is worse than no comment. If I see one, I delete it on the spot — not because it's a big deal, but because the next person who reads it will trust it. The same applies to my own old comments. I have no loyalty to past-me.
