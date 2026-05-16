---
layout: default
title: Error Handling
parent: Code I Prefer
nav_order: 6
---

# Error Handling
{: .no_toc }

Most bugs I hunt for hours turn out to be errors that were silently caught and discarded. So my rule is the opposite: throw at the boundary, handle at the UI, never swallow.

---

## Three layers, three jobs

| Layer | Job | What it looks like |
|:------|:----|:-------------------|
| **Boundary (API client, parsers)** | Convert raw errors into typed app errors | Wrap Axios errors into `ApiError` |
| **Domain (services, hooks)** | Let typed errors bubble up. Don't catch unless you can recover | No `try/catch` unless you have a real fallback |
| **UI (components)** | Show the user something. Always. | Error boundaries + per-query error states |

If a middle layer catches an error to log it and then re-throws, that's fine. If it catches an error to "be safe" and returns `null`, that's a bug I'll spend a week finding.

---

## Typed errors at the boundary

```ts
export class ApiError extends Error {
  constructor(
    public status: number,
    public code: string,
    message: string,
    public details?: unknown,
  ) {
    super(message);
    this.name = "ApiError";
  }
}

export class ValidationError extends Error {
  constructor(public issues: ZodIssue[]) {
    super("Validation failed");
    this.name = "ValidationError";
  }
}
```

Every catch in the app uses `instanceof` to discriminate:

```ts
try {
  await ordersService.create(payload);
} catch (e) {
  if (e instanceof ValidationError) showFieldErrors(e.issues);
  else if (e instanceof ApiError && e.status === 409) toast("Already exists");
  else throw e;  // let it bubble to the error boundary
}
```

---

## Don't try/catch for the sake of it

```ts
// Bad — swallows the error, returns nonsense
async function getUser(id: string) {
  try {
    return await usersService.get(id);
  } catch {
    return null;
  }
}

// Good — let the caller decide
async function getUser(id: string) {
  return await usersService.get(id);
}
```

The "bad" version forces every caller to check for `null` *and* still doesn't know whether `null` means "not found" or "server exploded." That's strictly worse than a thrown error with a status code.

---

## React Query owns retry + error state

Components don't `try/catch` around their `useQuery` calls. They consume the `status` / `error` from the query result:

```tsx
const { data, status, error } = useOrders(filters);

if (status === "pending") return <Skeleton />;
if (status === "error") return <ErrorBanner error={error} />;
return <OrdersList orders={data} />;
```

Retries are configured at the `QueryClient` (don't retry 4xx, retry 5xx twice). Components just render whatever state they're handed.

---

## Error boundaries are required, not optional

Every route gets an error boundary above its content. Without one, an unexpected throw crashes the entire app tree. With one, the user sees a "something went wrong, here's a refresh button" card and the rest of the app still works.

```tsx
// app/layout.tsx (Next.js) or _layout.tsx (Expo Router)
<ErrorBoundary fallback={<AppErrorScreen />}>
  <RouteContent />
</ErrorBoundary>
```

I also log the error from `componentDidCatch` / `onError` into Sentry so I find out about prod crashes without a user emailing me.

---

## Never `console.error` and move on

Production code never logs an error as its only response to it. Either:

1. **Handle it** — show the user, retry, fall back to cached data — *and* log it for diagnostics.
2. **Let it bubble** — the error boundary catches it and reports.

`console.error` alone means "I noticed something was wrong, then I pretended I didn't." That's the path to silent corruption.

---

## A practical checklist

Before I ship a feature, I ask:

- [ ] Does every async call have a defined "what does the user see if this fails?"
- [ ] Are 4xx and 5xx differentiated? (401 → re-auth, 403 → permission UI, 404 → empty state, 5xx → retry/banner)
- [ ] Is there an error boundary above this screen?
- [ ] If the network is offline, does the UI degrade gracefully or hang forever?
- [ ] Are errors being reported to Sentry (or equivalent) with enough context to debug?

If any of those are "no," the feature isn't done.
