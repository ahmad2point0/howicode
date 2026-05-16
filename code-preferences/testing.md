---
layout: default
title: Testing Philosophy
parent: Code I Prefer
nav_order: 8
---

# Testing Philosophy
{: .no_toc }

Tests are insurance. They're worth what they cost — no more. I'm not chasing 100% coverage; I'm trying to ship features I can change next month without breaking.

---

## The shape: more integration, fewer unit

I lean toward the **testing trophy** (Kent C. Dodds), not the pyramid:

```text
            🏆
       ┌─────────┐
       │   E2E   │   ← a handful — golden paths only
       ├─────────┤
       │   Int.  │   ← the bulk — features behaving end-to-end
       ├─────────┤
       │   Unit  │   ← only for genuinely complex pure functions
       ├─────────┤
       │ Static  │   ← TypeScript + ESLint, free coverage
       └─────────┘
```

A test that mounts a real component, lets it hit a mock-server route, and asserts the rendered output catches **way** more real bugs per line than ten unit tests that each pin one implementation detail.

---

## Test behavior, not implementation

The single rule that decides whether a test ages well:

> If I refactor the internals without changing the user-visible behavior, the test should still pass.

```ts
// Bad — pinned to implementation. Renaming setUser breaks the test.
expect(authStore.getState().setUser).toHaveBeenCalledWith({ id: "1" });

// Good — pinned to behavior. The user is now logged in.
expect(screen.getByText(/welcome, ahmad/i)).toBeInTheDocument();
```

I write tests from the perspective of someone using the feature, not someone reading the code.

---

## What I mock and what I don't

| Thing | Default |
|:------|:--------|
| The component under test | **Never** mock |
| Child components | **Almost never** — let them render |
| Network requests | Mock with **MSW** (a real HTTP layer, intercepted) |
| Database (Node tests) | **Real test DB**, reset between tests |
| Time, randomness | Mock — `vi.useFakeTimers()`, fixed seed |
| Third-party SDKs (Stripe, OpenAI) | Mock at the SDK boundary |

I avoid mocking the database in integration tests. A test that passes against a mock and fails against the real schema is worse than no test.

---

## React tests use Testing Library, not Enzyme

`@testing-library/react` (web) and `@testing-library/react-native` (mobile). Queries in priority order:

1. `getByRole` — accessible roles (most resilient)
2. `getByLabelText` — for form inputs
3. `getByText` — for buttons, links, copy
4. `getByTestId` — **last resort**, when nothing else works

If I have to reach for `getByTestId`, that's usually a hint the component isn't accessible — and the right fix is often adding the missing `aria-label`, not the test id.

---

## A test file looks like this

```ts
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { LoginForm } from "./LoginForm";

describe("LoginForm", () => {
  it("logs the user in with valid credentials", async () => {
    const user = userEvent.setup();
    render(<LoginForm />);

    await user.type(screen.getByLabelText(/email/i), "a@b.com");
    await user.type(screen.getByLabelText(/password/i), "hunter2");
    await user.click(screen.getByRole("button", { name: /sign in/i }));

    expect(await screen.findByText(/welcome back/i)).toBeInTheDocument();
  });

  it("shows an error for an invalid password", async () => {
    // ...
  });
});
```

One `describe` per component, one `it` per behavior. No nested describes unless there are genuinely different contexts (e.g., logged-in vs logged-out).

---

## E2E: Playwright for web, Maestro for mobile

I keep the E2E suite small and ruthless — only the user journeys that, if they broke in prod, would actually hurt:

- Sign up → log in → log out
- The top one or two "money paths" (checkout, send message, upload file)
- Anything regulated (payments, account deletion)

Everything else is integration. E2E is slow, flaky-prone, and expensive to maintain — I pay that cost only for the parts that need it.

---

## When I skip tests

I don't write tests for:

- Prototypes I'm about to throw away (and I'm honest with myself about whether it's really a prototype)
- Tiny one-off scripts that will be run once
- Generated code (Prisma client, OpenAPI stubs)
- Trivial getters/setters and pure passthroughs

Everything else gets at least one test that exercises its happy path. If I can't think of a single behavior worth asserting, the code probably shouldn't exist either.
