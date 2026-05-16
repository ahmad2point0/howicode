---
layout: default
title: TypeScript Rules
parent: Code I Prefer
nav_order: 1
---

# TypeScript Rules
{: .no_toc }

TypeScript is non-negotiable for me. Plain JS only happens when the platform forces it (a config file, a tiny script). Everywhere else, strict TypeScript.

---

## Always start with strict mode

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "noFallthroughCasesInSwitch": true,
    "exactOptionalPropertyTypes": true
  }
}
```

`strict: true` is the floor, not the ceiling. The extras above catch the bugs that `strict` misses — indexed access returning `undefined`, missed `override` keywords, fall-through `case` blocks.

---

## `any` is a code smell, not a tool

If you must use `any`, you owe a comment explaining why and a `TODO` to remove it. In 90% of cases, the right escape hatch is `unknown` plus a narrowing check:

```ts
// Bad — silently allows anything downstream
function parse(raw: any) { return raw.user.name; }

// Good — forces the caller to narrow before use
function parse(raw: unknown): string {
  if (
    typeof raw === "object" && raw !== null &&
    "user" in raw && typeof (raw as any).user?.name === "string"
  ) {
    return (raw as { user: { name: string } }).user.name;
  }
  throw new Error("Invalid shape");
}
```

For external data (API responses, form input, env vars), I reach for **Zod** rather than hand-rolled guards.

---

## Prefer `type` for unions, `interface` for objects you extend

It's a soft rule, but it pays off in error messages:

```ts
type Status = "idle" | "loading" | "success" | "error";

interface User {
  id: string;
  name: string;
  email: string;
}

interface AdminUser extends User {
  permissions: string[];
}
```

---

## Types live with the feature

I don't keep a global `types/` folder full of unrelated types. Each feature owns its own `@types/` directory:

```text
features/auth/
├── @types/
│   ├── user.ts
│   ├── session.ts
│   └── index.ts
```

Shared cross-cutting types (API envelope, pagination, etc.) go in `global/@types/`.

---

## Inference > annotation, except at boundaries

I let TypeScript infer return types inside a feature, but I annotate **public** function signatures and component prop types. The reasoning: explicit types at boundaries are documentation; explicit types in internals are noise.

```ts
// Internal helper — let inference do its job
const computeTotal = (items: CartItem[]) =>
  items.reduce((sum, it) => sum + it.price * it.qty, 0);

// Public — annotate the boundary
export function createCart(items: CartItem[]): Cart {
  return { items, total: computeTotal(items) };
}
```

---

## Discriminated unions for state

Loading states are unions, not flag combinations. Three booleans (`isLoading`, `isError`, `isSuccess`) is a bug factory.

```ts
type Query<T> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "error"; error: Error }
  | { status: "success"; data: T };
```

The compiler then forces you to handle every branch — and `data` only exists in the `success` case, where you actually have it.
