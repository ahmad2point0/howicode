---
layout: default
title: Code I Prefer
nav_order: 2
has_children: true
permalink: /code-preferences/
---

# Code I Prefer
{: .fs-9 }

The short, opinionated version of how I write code — the defaults I reach for before I think.
{: .fs-6 .fw-300 }

---

## Why this exists

Every codebase eventually answers the same questions: how do we name things? Where does state live? How do we talk to APIs? Who owns errors? I want to answer them once, write it down, and stop re-deciding.

This section is not a style guide for a team — it's a snapshot of my personal defaults. If you disagree, you're probably right for your project. These work for mine.

---

## The defaults at a glance

| Question | My default |
|:---------|:-----------|
| Language | TypeScript, `strict: true`, no `any` without a comment |
| Files | `kebab-case.ts`, components in `PascalCase.tsx` |
| State (client) | Zustand — small slices, no global god-store |
| State (server) | TanStack Query — never store server data in Zustand |
| HTTP | One Axios instance, interceptors handle auth + errors |
| Errors | Throw at the boundary, handle at the UI, never swallow |
| Comments | Explain **why**, never **what** |
| Tests | Test behavior, not implementation. Integration > unit when in doubt |
| Folders | Feature-based, barrel `index.ts` per feature |
| Imports | Absolute via `@/` alias, never `../../../` |

---

## Read in order

The pages below go from the smallest-grain decisions (naming) up to the largest (architecture). Each is short on purpose — under 5 minutes.

1. [TypeScript Rules](typescript.html)
2. [Naming & File Organization](naming.html)
3. [State Management](state.html)
4. [API & Data Fetching](api.html)
5. [Styling](styling.html)
6. [Error Handling](error-handling.html)
7. [Comments & Documentation](comments.html)
8. [Testing Philosophy](testing.html)
