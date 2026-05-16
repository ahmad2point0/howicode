---
layout: default
title: Naming & File Organization
parent: Code I Prefer
nav_order: 2
---

# Naming & File Organization
{: .no_toc }

Names are 60% of code quality. If I have to read the function body to know what it does, the name has failed.

---

## File naming

| Kind | Convention | Example |
|:-----|:-----------|:--------|
| Components | `PascalCase.tsx` | `LoginForm.tsx` |
| Hooks | `use-kebab-case.ts` | `use-auth.ts` |
| Services / utils | `kebab-case.ts` | `auth-service.ts` |
| Types | `kebab-case.ts` | `user.ts` |
| Tests | `*.test.ts(x)` | `LoginForm.test.tsx` |
| Routes (Expo / Next) | framework defaults | `(tabs)/index.tsx` |

I don't fight the framework. Expo Router and Next.js App Router have opinions about route filenames — I follow them, no matter what the rest of the project looks like.

---

## Variable & function naming

- **Booleans** start with `is`, `has`, `should`, `can` → `isLoading`, `hasAccess`, `shouldRetry`
- **Functions** start with a verb → `getUser`, `fetchOrders`, `formatDate`
- **Event handlers** start with `handle` for definitions, `on` for props → `handleSubmit` defined, `onSubmit` as a prop name
- **Async actions** end with the noun, not "promise" or "async" → `loadUser`, not `loadUserAsync`
- **Constants** are `SCREAMING_SNAKE_CASE` only when truly constant (env-derived or magic numbers). Module-level config objects stay `camelCase`.

---

## Folder structure — feature-first, always

```text
src/
├── app/                # Routing only — thin
├── features/
│   ├── auth/
│   │   ├── components/   # Feature-local UI
│   │   ├── hooks/        # Feature-local hooks
│   │   ├── services/     # API calls, business logic
│   │   ├── store/        # Zustand slice for this feature
│   │   ├── @types/       # Feature-local types
│   │   └── index.ts      # Public API — what the rest of the app can import
│   ├── chat/
│   └── orders/
└── global/
    ├── components/       # Truly shared UI (Button, Input, Modal)
    ├── hooks/            # Cross-cutting hooks (useDebounce, useMediaQuery)
    ├── utils/
    ├── store/            # App-wide stores (theme, auth-session)
    ├── config/
    └── @types/
```

### Three hard rules

1. **Screens are thin.** A route file imports feature components and composes them — no fetching, no business logic, no Zustand calls.
2. **Features don't cross-import.** If `chat` needs something from `auth`, that something belongs in `global/`. Otherwise you've just built a graph of hidden coupling.
3. **Every feature exposes a barrel.** Consumers import from `@/features/auth`, never from `@/features/auth/components/LoginForm`. The barrel is the feature's contract.

---

## Imports

- Absolute imports via `@/` alias — never `../../../`
- Order: `react` / framework → third-party → `@/global` → `@/features` → relative → styles
- One blank line between groups; many editors enforce this automatically

```ts
import { useState } from "react";
import { useQuery } from "@tanstack/react-query";

import { Button } from "@/global/components";
import { useAuthStore } from "@/features/auth";

import { useChatScroll } from "./hooks/use-chat-scroll";
import styles from "./ChatWindow.module.css";
```

---

## What goes in `global/`

A new file goes in `global/` only when **two or more features need it**. Speculative "we'll probably need this everywhere" code stays inside the feature that first uses it. Move it up the day a second feature reaches for it.
