---
layout: default
title: API & Data Fetching
parent: Code I Prefer
nav_order: 4
---

# API & Data Fetching
{: .no_toc }

One Axios instance. One place that knows how to authenticate. One place that knows how to retry. Every component just calls a service function and gets data — they shouldn't know about headers, tokens, or refresh logic.

---

## One Axios instance, configured once

I never `import axios from "axios"` inside a component. I import a pre-configured instance from `global/api/`:

```ts
// global/api/axios-instance.ts
import axios from "axios";

export const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  timeout: 10_000,
  headers: { "Content-Type": "application/json" },
});
```

This makes it trivial to add cross-cutting concerns: auth, logging, request IDs, retries — all in one place.

---

## Services own the URL, components own the UI

Components don't know endpoint paths. They call a service function that returns typed data:

```ts
// features/orders/services/orders-service.ts
import { apiClient } from "@/global/api";
import type { Order, OrderFilters } from "../@types";

export const ordersService = {
  list: (filters: OrderFilters) =>
    apiClient.get<Order[]>("/orders", { params: filters }).then((r) => r.data),

  get: (id: string) =>
    apiClient.get<Order>(`/orders/${id}`).then((r) => r.data),

  create: (payload: Omit<Order, "id">) =>
    apiClient.post<Order>("/orders", payload).then((r) => r.data),
};
```

The service is the only file that knows the endpoint shape. If the API changes, I update one place.

---

## React Query wraps the service

```ts
// features/orders/hooks/use-orders.ts
import { useQuery } from "@tanstack/react-query";
import { ordersService } from "../services/orders-service";

export const useOrders = (filters: OrderFilters) =>
  useQuery({
    queryKey: ["orders", filters],
    queryFn: () => ordersService.list(filters),
  });
```

The hook is what components use. The service is pure data access. The two-layer split is worth the extra file — it lets me test services without React and reuse them from background jobs, server actions, or scripts.

---

## Auth lives in interceptors, not in every call

A request interceptor attaches the access token; a response interceptor handles 401s by refreshing and retrying. I write this once per project and never touch it from a component.

The full pattern — including the queue that prevents N parallel refresh calls — is in the JWT post:
**[Auto-refreshing JWTs with Axios →](../blogs/jwt-refresh-tokens-axios.html)**

---

## Errors get typed and thrown

Axios errors are not friendly. I wrap them into a project-level error type at the response interceptor so the rest of the app deals with one shape:

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

apiClient.interceptors.response.use(
  (r) => r,
  (error) => {
    if (error.response) {
      const { status, data } = error.response;
      throw new ApiError(
        status,
        data?.code ?? "UNKNOWN",
        data?.message ?? "Request failed",
        data,
      );
    }
    throw new ApiError(0, "NETWORK", "Network error");
  },
);
```

Now every catch block can rely on `instanceof ApiError` and discriminated `error.code`s instead of digging through `error.response.data.message`.

---

## Mutations: optimistic when it matters, pessimistic otherwise

For high-frequency UI (likes, toggles, drag-and-drop reorders), I use optimistic updates so the UI responds instantly:

```ts
const { mutate } = useMutation({
  mutationFn: ordersService.update,
  onMutate: async (next) => {
    await queryClient.cancelQueries({ queryKey: ["orders"] });
    const prev = queryClient.getQueryData<Order[]>(["orders"]);
    queryClient.setQueryData<Order[]>(["orders"], (old) =>
      old?.map((o) => (o.id === next.id ? next : o)),
    );
    return { prev };
  },
  onError: (_e, _v, ctx) => {
    if (ctx?.prev) queryClient.setQueryData(["orders"], ctx.prev);
  },
  onSettled: () => queryClient.invalidateQueries({ queryKey: ["orders"] }),
});
```

For destructive or financial actions (delete account, submit payment), I stay pessimistic — wait for the server, then update. The UX cost of a 200ms spinner is much smaller than the cost of "your payment looked successful but actually wasn't."

---

## The end-to-end shape

```text
features/orders/
├── services/orders-service.ts   # axios calls, returns typed data
├── hooks/use-orders.ts          # React Query wrappers
├── hooks/use-create-order.ts    # mutations
├── components/OrdersList.tsx    # consumes the hook
└── @types/order.ts              # the Order type
```

Each layer has one job. When something breaks, I know exactly which file to open.
